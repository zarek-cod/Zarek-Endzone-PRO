# Auditoría técnica — ZAREK ENDZONE PRO v0.1.1

Fecha de auditoría: 2026-11-09  
Estado: parcial; código FC compilado y estructura revisada, activos del editor pendientes de Craftland Studio MCP.

## Resumen ejecutivo

El proyecto conserva la arquitectura de la plantilla **Last Safe Zone** y contiene cambios experimentales de ZAREK. La lógica principal de rondas, zonas, puntuación, apariciones, equipamiento, tienda e interfaces está presente. Sin embargo, el estado real no coincide con toda la documentación: la configuración activa usa 3 rondas, no 16; los marcadores personalizados siguen limitados a 3 filas; y el cambio de zonas 45/25/19 no está aplicado.

No se modificó lógica de juego durante esta auditoría. El primer intento de compilación quedó bloqueado porque faltaba `Temp/UGCLanguage/editorGen/EditorGenSymbol.json`; después de abrir Craftland Studio se regeneró `editorGen` y la compilación completa terminó con código 0 y advertencias, sin errores.

## Inventario confirmado

- Proyecto: `main.gproj`, nombre interno `Zarek Endzone Base`.
- Versión declarada por el proyecto: `1.19.4689915`.
- Código: 35 archivos `.fcg` y 1 archivo `.fcc` bajo `Assets/Scripts/`.
- Escenarios referenciados por código: 11 grupos de aparición, 11 centros de zona y 11 grupos de puntos finales.
- Compilador configurado y existente: `C:\Program Files\Craftland Studio\resources\LocalData\Utilities\UGCLanguage\release\fccompile.exe`.
- Todos los imports relativos entre scripts `.fcg` apuntan a archivos existentes.
- No se encontraron nombres de graph duplicados.
- Los 35 scripts `.fcg` importan `EditorGenLib.fcc`; la copia generada ya está disponible en `Temp/UGCLanguage/editorGen/`.
- 195 referencias `ERes*.<miembro>` fueron contrastadas: 191 activas/generadas confirmadas y 4 referencias ausentes únicamente dentro de comentarios obsoletos.

## Estado real de los cambios ZAREK

| Área | Estado confirmado en código | Diferencia o bloqueo |
| --- | --- | --- |
| Rondas | `esportMaxRound = 3` | No están activas 16 rondas. |
| Preparación | 30 s en primera y siguientes rondas | `ZAREK_INSTRUCCIONES.md` indica 30/25 s; el código usa 30/30 s. |
| Paredes gloo | 20 por ronda | Aplicado mediante `GlobalConfig`. |
| Granadas | 2 por ronda | Aplicado mediante `GlobalConfig`. |
| Botiquines | 3 por ronda | Aplicado mediante `GlobalConfig`. |
| Inhaladores | 2 por ronda | Aplicado mediante `GlobalConfig`. |
| Apariciones | Rotación adaptable al número de posiciones | La cantidad y seguridad de las posiciones del escenario no se pudo verificar sin MCP. |
| Equipos activos | Detección dinámica por jugadores conectados | Los ajustes reales de `TeamCount`, `MemberCount` y mínimo de inicio están pendientes del editor. |
| Puntuación | 12/9/8/7/6/5/4 por posición más bajas | Aplicada en `GlobalGameManager`; requiere pruebas de casos límite. |
| Mini/full scoreboard | Protecciones para no indexar filas inexistentes | Los activos personalizados siguen teniendo únicamente 3 filas. |
| Tienda | Abre el primer Shop configurado por el editor | ZAREK ARMORY no está implementada ni verificada. |
| Zonas | Valores por escenario: inicio 15–50, primer cierre 12–25, reducción móvil 5, cierre final 0 | La propuesta 45/25/19 no está aplicada. |

El registro generado `EResItem` confirma actualmente: `VestLv3`, `HelmetLv3`, `GlooWall`, `AK47`, `XM8`, `GROZAX`, `M590`, `MedKit`, `SuperMed`, `Inhaler`, `Grenade`, `FlashFreeze`, `GlooMelter`, accesorios, `M4A1`, `AUG`, `FAMAS`, `SCAR`, `HEAL_PISTOL` y `WOODPECKER`. No aparecen registrados AWM, M82B, M24, Kar98k, M1887, SPAS12, M1014, MAG-7, Charge Buster, Trogón, MP40, UMP, MP5, Thompson, P90, Vector, MAC10, AC80, SVD ni granada de humo.

## Flujo de juego observado

1. `GlobalPreparationPhaseManager` elige uno de 11 escenarios, incrementa el contador, prepara inventario y teletransporta equipos activos.
2. `GlobalSafeZones` activa una zona de preparación y después tres fases de combate: cierre estático, traslado con reducción y cierre final; `SuddenDeath` usa radio 0.
3. `GlobalCombatManager` registra eliminaciones, daño y orden de escuadras eliminadas.
4. `GlobalGameManager` asigna puntos, muestra resultados y decide el final al alcanzar `GlobalConfig.esportMaxRound`.
5. `GlobalLeaderboard` persiste estadísticas y rankings en Data Store.

La API oficial local confirma que `EnableSafeZone` y `EnableMovingSafeZone` reciben radios `float`, duraciones en segundos y daño por segundo. La documentación local no nombra explícitamente la unidad espacial del radio; no se asumirá que equivale a metros hasta verificar el escenario en Craftland Studio.

## Hallazgos prioritarios

### Críticos o altos

1. `GlobalGameManager.CombatEnd` cambia de fase si `winningTeam == nil`, pero continúa y desreferencia `winningTeam` después. Debe retornar o separar el flujo sin ganador.
2. `NoTeamWinException` usa `GetAllTeams()[0]` y concede un Booyah a ese equipo arbitrario aunque no haya ganador. También falla si la lista está vacía.
3. `GlobalUIManager.EnableRoundStartBanner(player)` crea la UI para `thisEntity<Player>` en vez del parámetro `player`; el graph es global y esa conversión no es coherente con el destino esperado.
4. `PlayersShopIcon.shopIconSize` se declara pero no se inicializa antes de escribirse en `RegisterClientShopIconDimension`.
5. El sistema de leaderboard contiene varios bucles de reintento sin límite. Un fallo persistente del servicio puede dejar tareas bloqueadas indefinidamente.

### Medios

1. `GetAllShops()[0]`, varios resultados de `AddItemToPlayer()[0]` y algunos puntos finales de zona se indexan sin comprobar longitud.
2. La UI muestra como “posición” el índice de eliminación en ciertas animaciones; ese orden es inverso a la posición competitiva.
3. `GlobalSafeZones.SetStartShrinkTime(prepDuration)` ignora el parámetro. Los valores calculados solo se registran en logs; la zona de preparación usa `waitTime = 90` fijo.
4. Los logs de zona no coinciden con la ejecución: fase 1 registra duración 5 pero usa 33; fase 3 registra 60 pero usa 30.
5. `RandomStage` conserva todos los escenarios anteriores, no solo los recientes; tras agotar valores únicos termina repitiendo el último escenario.
6. `PlayerController` contiene IDs crudos `534` y `535` convertidos a `ItemIDType`. Deben sustituirse únicamente después de confirmar el símbolo oficial equivalente.
7. El leaderboard aplica una caducidad real de 14 días (`1209600` s), mientras el README anterior describe 12 minutos. Además, después de limpiar escribe `diff` como timestamp, lo que debe revisarse contra la intención de persistencia.
8. Hay módulos obsoletos aún importados (`TeamPoints`, `TeamsAlive`, `PlayersTimerUI`, `GlobalCurrencyManager`) y código de prueba activo (`Test.fcg`). Sus attachments solo pueden confirmarse en el editor.

### Documentación y configuración

- `README.md` todavía identifica el juego como Last Safe Zone, 3 equipos y 5 rondas en varias secciones.
- `ZAREK_INSTRUCCIONES.md` afirma 16 rondas y compilador no disponible; en este equipo el compilador sí existe, pero falta `editorGen`.
- `main.gproj` conserva el nombre interno `Zarek Endzone Base`.
- No existe licencia definida; el README mantiene el marcador `[Specify your license here]`.

## Resultado del compilador

La compilación completa se ejecutó con:

```text
C:\Program Files\Craftland Studio\resources\LocalData\Utilities\UGCLanguage\release\fccompile.exe -i Assets -agent unknown-model -session 2176be10-916b-4600-aa2f-e8e98ba1971e
```

Resultado: código de salida `0`, sin errores. Las advertencias se concentran en:

- APIs y propiedades obsoletas (`ClearResidualLevelObjects`, `UIWidget.Active` y varias APIs antiguas de Database);
- comparación incompatible entre `entity<Entity>` y `EResItem` en `PlayerController.fcg:94`;
- sombras de `isDeleting`, `isDeletingStatusCode` y `overallStatusCode` en `GlobalLeaderboard.fcg`.

Estas advertencias no bloquean la compilación, pero no deben confundirse con mensajes meramente informativos: la comparación incompatible y las sombras justifican corrección controlada.

## Activos y límites no verificados

Craftland Studio MCP no estaba conectado durante la auditoría. Por esa razón no se inspeccionaron ni alteraron directamente `.eca`, `.scene`, `.ui`, `.asset`, `.cproj`, `.wfl`, prefabs ni registros de recursos. Quedan pendientes:

- límites reales de jugadores/equipos y mínimo de inicio;
- duración y orden del Workflow;
- attachments de todos los graphs;
- cantidad y transform de puntos de aparición y finales de zona;
- registro actual de `ERes*` y disponibilidad del catálogo de armas;
- filas y callbacks de los activos UI;
- límites jugables de cada uno de los 11 escenarios.

## Publicación segura

Se excluyen de Git los temporales, cachés, endpoints MCP locales, librerías distribuidas por Craftland/Garena, contenido incorporado y recursos multimedia cuya procedencia no está documentada. No hay archivos individuales de 100 MB o más; Git LFS no es obligatorio para el conjunto publicable actual.

La exclusión de recursos multimedia implica que una clonación no es todavía una copia autónoma del proyecto. Antes de añadirlos debe documentarse autor y permiso de redistribución de cada archivo.

## Próxima secuencia recomendada

1. Abrir y guardar el proyecto en Craftland Studio para regenerar `Temp/UGCLanguage/editorGen/`.
2. Conectar Craftland Studio MCP y leer `instructions://index`.
3. Verificar ajustes de equipos, Workflow, registros, UI y geometría de los 11 escenarios.
4. Corregir primero los fallos de flujo/nil anteriores en una rama y validar.
5. Aplicar un único cambio de zona compatible con los límites reales y probar todas las fases.
