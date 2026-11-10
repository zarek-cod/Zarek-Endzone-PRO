# Validación — ZAREK ENDZONE PRO

Última actualización: 2026-11-10.

## Entorno

- Sistema: Windows.
- Git: `2.55.0.windows.3`.
- GitHub CLI: `2.103.3` instalado durante esta sesión.
- Craftland Studio declarado por `main.gproj`: `1.19.4689915`.
- Compilador FC configurado: `C:\Program Files\Craftland Studio\resources\LocalData\Utilities\UGCLanguage\release\fccompile.exe`.
- Identificador de sesión de compilación: `2176be10-916b-4600-aa2f-e8e98ba1971e`.
- Modelo reportado al compilador: `unknown-model`, porque el runtime no expone un identificador de modelo exacto reutilizable.

## Validaciones ejecutadas

### Validación final del orden de abandono y cierre del PR #1

Archivos FC modificados en esta revisión:

- `Assets/Scripts/Global/GlobalManager/GlobalGameManager.fcg`;
- `Assets/Scripts/Global/GlobalManager/GlobalCombatManager.fcg`;
- `Assets/Scripts/Global/GlobalManager/GlobalUIManager.fcg`;
- `Assets/Scripts/Global/GlobalData/TeamData.fcg`;
- `Assets/Scripts/Team/Team.fcg`.

Evidencia estática y del SDK oficial instalado:

- `Workflow.fcc` expone `globalEntity<Global>.CurrentPhaseIndex` como propiedad de solo lectura; no se inventó una API de fase.
- `Combat.fcc` documenta que `EliminateAtNextFrame` elimina en el siguiente frame. La implementación espera dos frames antes de leer equipos: el frame objetivo y un frame adicional para estabilizar `IsAced`/`OnACE`.
- Se registra si la salida comenzó durante combate y se vuelve a comprobar la fase tras la espera. Un abandono iniciado en `Preparation` no entra en `MatchEndHandler` ni solicita `CombatEnd`.
- La lista permitida se deriva del enum generado y de los scripts FC actuales: `FirstShrinkPrep`, `Combat`, `MovingSafeZone`, `LastSafeZone` y `SuddenDeath`. `FirstShrinkPrep` se incluye porque el flujo actual quita la invencibilidad en esa fase; `SuddenDeath` aplica la zona final.
- `CheckEndCombatCondition` aplica el guard de fase centralmente, por lo que también protege llamadas originadas en `Team.OnACE`.
- `OnACE` registra el equipo de forma deduplicada y solicita una comprobación agrupada. Dos esperas de frame permiten acumular eliminaciones simultáneas antes de decidir entre `CombatEnd` y `NoTeamWinException`.
- `isChecked` se fija antes de despachar cualquiera de los dos eventos y `isCheckScheduled` agrupa solicitudes; las llamadas posteriores no pueden volver a despachar el cierre.
- BOOYAH_COUNT conserva el final por única escuadra conectada, pero primero reclama el mismo cierre idempotente. ESPORT sigue resolviendo la ronda y el campeón final mediante su clasificación acumulada.
- `NoTeamWinException` mantiene `isRoundEnded = true` durante el procesamiento. Su ruta continúa usando `UpdateFullScoreBoardNoWinner`, que no ejecuta animaciones de vencedor cuando `wonTeam == nil`.
- La animación de posición y la asignación de puntos comparten `TeamData.GetPlacementPointsForPosition`; ganador y eliminados muestran exactamente el incremento que se añadió al acumulado, también en la ruta sin supervivientes.
- No se cambiaron los valores de puntuación, persistencia ni desempates implementados en `f22e4ca`; solo se alineó la animación del marcador con esos valores existentes.
- Se confirmaron `esportMaxRound = 3`, `glooWallsPerRound = 20` y ausencia de cambios en scripts de zonas.

Comprobaciones ejecutadas:

```text
git diff --check
"C:\Program Files\Craftland Studio\resources\LocalData\Utilities\UGCLanguage\release\fccompile.exe" -i Assets
```

Resultado:

- `git diff --check`: aprobado;
- compilación FC: código de salida `0`;
- errores: ninguno;
- advertencias: 66 encabezados, todos de familias heredadas por `ClearResidualLevelObjects()`, `UIWidget.Active`, APIs antiguas de Database/Leaderboard, comparación `entity<Entity>`/`EResItem` y sombreado de variables en `GlobalLeaderboard.fcg`;
- no aparecieron diagnósticos nuevos en los cinco archivos modificados.

Limitación del editor:

- Craftland Studio está abierto y su proceso confirma este mismo `main.gproj` (`Zarek Endzone Base`). Sin embargo, Craftland Studio MCP no está expuesto a esta sesión, por lo que no fue posible leer `instructions://index` ni inspeccionar el Workflow, attachments o configuración de equipos desde el editor.
- `ProjectSettings/Workflow.wfl` y los recursos serializados no se leyeron ni modificaron directamente.
- No se ejecutaron build del editor, consola ni pruebas en partida. El resultado runtime no se declara aprobado.

Escenarios runtime pendientes en Craftland Studio:

1. Abandono durante `Preparation`: confirmar que no termina ronda ni partida.
2. Abandono durante cada fase activa, incluido `FirstShrinkPrep` y `SuddenDeath`: confirmar una sola resolución después de actualizar `IsAced`.
3. Eliminación simultánea de todos los equipos: confirmar `NoTeamWinException`, marcador completo sin animación de vencedor ni Booyah y una sola aplicación de puntos.
4. Tercer cierre sin ganador: confirmar campeón por acumulado, persistencia única y transición a `End`.
5. Una sola escuadra conectada: confirmar comportamiento BOOYAH_COUNT y, en ESPORT, campeón por clasificación acumulada.
6. Desempate y final normal: confirmar una sola llamada final, orden de desempate y ausencia de escrituras duplicadas.

### Correcciones posteriores a la revisión del PR #1

Archivos FC modificados en esta revisión:

- `Assets/Scripts/Global/GlobalManager/GlobalGameManager.fcg`;
- `Assets/Scripts/Global/GlobalManager/GlobalUIManager.fcg`;
- `Assets/Scripts/Global/GlobalManager/GlobalCurrencyManager.fcg`;
- `Assets/Scripts/Global/GlobalData/TeamData.fcg`.

Comprobaciones estáticas:

- `NoTeamWinException` inicia `UpdateFullScoreBoardNoWinner` después de aplicar kills y puntos de posición y antes de pasar a `EndCombat`.
- Todas las ramas de animación específicas del vencedor requieren `wonTeam != nil`; una ronda sin ganador actualiza el total y aplica incremento de Booyah igual a cero.
- En ESPORT, `OnPhaseStart(EPhase.End)` siempre usa `GetSortedTeamList` y `GetAllWinningTeams`, sin declarar campeón al único equipo conectado.
- En ESPORT, `OnPlayerQuit` con una sola escuadra conectada llama a `CheckEndCombatCondition` y no a `MatchEndHandler`; el atajo de fin inmediato queda limitado a BOOYAH_COUNT.
- BOOYAH_COUNT conserva el caso especial de una única escuadra activa.
- El orden ESPORT es puntos totales → Booyahs → kills → puntos de posición; la conexión dejó de formar parte del desempate.
- `GetAllWinningTeams` solo devuelve varios campeones cuando esos cuatro valores son exactamente iguales.
- Las llamadas finales a `StorePlayerDataToDatabase` se centralizaron en `StoreFinalPlayerData` y el flag `finalStatisticsStored` se activa antes de lanzar escrituras asíncronas.
- La API activa guarda kills, muertes, partidas y victoria. No existe un parámetro confirmado para persistir puntos de equipo, por lo que no se inventó una escritura adicional; los puntos permanecen en `TeamData` para clasificación y desempate.
- `GlobalCurrencyManager.CombatEnd` retorna ante `winningTeam == nil`, antes de repartir moneda a vencedores o perdedores.
- `git diff --check`: aprobado.
- Se confirmaron `esportMaxRound = 3`, `glooWallsPerRound = 20` y ausencia de cambios en scripts de zona.

Compilación completa:

```text
"C:\Program Files\Craftland Studio\resources\LocalData\Utilities\UGCLanguage\release\fccompile.exe" -i Assets -agent unknown-model -session 2176be10-916b-4600-aa2f-e8e98ba1971e
```

Resultado de la última ejecución:

- código de salida: `0`;
- errores: ninguno;
- advertencias: las mismas familias heredadas —`ClearResidualLevelObjects()`, `UIWidget.Active`, APIs antiguas de Database/Leaderboard, comparación `entity<Entity>`/`EResItem` y sombreado de variables—;
- no aparecieron diagnósticos nuevos en los cuatro archivos modificados.

Comprobación de attachment:

- Craftland Studio MCP no está disponible en esta sesión; no fue posible leer `instructions://index`, `asset-entity-search` ni `asset-entity-get`.
- `EditorGenLib.fcc` confirma que `GlobalCurrencyManager` está registrado como recurso FC, pero eso no demuestra que esté adjunto a una entidad.
- El script y `PlayersWallets.fcg` se describen como obsoletos, y su única llamada textual desde `PlayerController.fcg` está comentada.
- No se inspeccionaron ni modificaron `.eca`, escenas, attachments o configuraciones serializadas.

Escenarios runtime pendientes en Craftland Studio:

1. Eliminación simultánea: confirmar que no aparece Booyah y que kills, posición y total se actualizan una sola vez.
2. Tercer cierre sin ganador: confirmar campeón por acumulado, persistencia única y transición completa a `End`.
3. Una sola escuadra conectada en ESPORT: confirmar que pierde si otra escuadra desconectada tiene mejor clasificación acumulada.
4. Desempate: comprobar total → Booyahs → kills → posición y empate múltiple solo con igualdad exacta.
5. Final normal: comprobar una sola actualización de marcador, recompensas de moneda solo con ganador real y una sola escritura de estadísticas por jugador.

También queda pendiente verificar con MCP si `GlobalCurrencyManager` está adjunto y si existe más de un attachment, lo que podría duplicar listeners de moneda.

### Corrección `fix/estabilidad-combate`

Archivos FC modificados:

- `Assets/Scripts/Global/GlobalManager/GlobalGameManager.fcg`;
- `Assets/Scripts/Global/GlobalData/PlayersShopIcon.fcg`;
- `Assets/Scripts/Shop/ShopIconHUD.fcg`.

Comprobaciones estáticas:

- `CombatEnd` contiene una salida inmediata antes de cualquier acceso a un `winningTeam` nulo.
- No quedan coincidencias directas de `GetAllTeams()[0]` ni `GetAllShops()[0]` en los scripts FC.
- `NoTeamWinException` itera `teamRanking` sin indexarla: una lista vacía no produce accesos fuera de rango.
- `GetAllWinningTeams` conserva su protección `List.Length(...) <= 1` antes del primer acceso `[0]`.
- `shopIconSize` se inicializa tanto en `OnGameStart` como defensivamente en `RegisterClientShopIconDimension`.
- `git diff --check`: aprobado.
- Se confirmó `esportMaxRound = 3`, `glooWallsPerRound = 20` y ausencia de cambios en archivos de zona.

Compilación completa desde la raíz del proyecto:

```text
"C:\Program Files\Craftland Studio\resources\LocalData\Utilities\UGCLanguage\release\fccompile.exe" -i Assets -agent unknown-model -session 2176be10-916b-4600-aa2f-e8e98ba1971e
```

Resultado:

- código de salida: `0`;
- errores: ninguno;
- advertencias: permanecen las familias ya presentes en la línea base —`ClearResidualLevelObjects()`, `UIWidget.Active`, APIs antiguas de Database/Leaderboard, comparación `entity<Entity>`/`EResItem` y sombreado de variables en `GlobalLeaderboard.fcg`—;
- no aparecieron diagnósticos en `PlayersShopIcon.fcg` ni `ShopIconHUD.fcg`.

Validación de editor pendiente:

- Craftland Studio MCP no está disponible para esta sesión, por lo que no se ejecutaron `game-build` ni `game-console-get-logs`.
- No se modificaron recursos binarios, escenas, UI, workflows, attachments ni configuraciones del editor.
- Deben probarse en Craftland: final simultáneo sin supervivientes, tercera ronda sin ganador, actualización visual de la clasificación, apertura de tienda configurada y comportamiento cuando no existe una tienda.

### Inventario y dependencias

- 35 archivos `.fcg` y 1 `.fcc` localizados.
- Imports relativos `.fcg`: 0 destinos faltantes.
- Graphs duplicados: 0.
- Imports estándar comprobados contra el `CorePath` instalado.
- Búsqueda de patrones habituales de secretos en archivos de texto: sin coincidencias de credenciales.
- Se detectaron URLs MCP locales con una ruta específica de sesión en `.cursor/mcp.json` y `.vscode/mcp.json`; ambos archivos quedaron excluidos de Git.

### Compilación FC base — primer intento

Comando ejecutado desde la raíz:

```text
fccompile.exe -i Assets -agent unknown-model -session 2176be10-916b-4600-aa2f-e8e98ba1971e
```

Resultado: **no llegó a compilar los scripts**.

Diagnóstico:

```text
LoadSymbolFile failed, file not found: Temp\UGCLanguage\editorGen\EditorGenSymbol.json
```

Código de salida: `1`.

Interpretación: el compilador existía, pero Craftland Studio todavía no había generado las bibliotecas/símbolos del proyecto.

### Compilación FC base — segundo intento

Después de abrir Craftland Studio se confirmaron:

- `Temp/UGCLanguage/editorGen/EditorGenSymbol.json`;
- `Temp/UGCLanguage/editorGen/EditorGenLib.fcc`;
- `Temp/UGCLanguage/fcconfig.json`, incluido `MinimumExtensionVersion = 1.6.0`.

Se repitió el mismo comando completo y con el mismo identificador de sesión.

Resultado: **compilación completada**, código de salida `0`.

Advertencias principales:

- uso de `ClearResidualLevelObjects()` obsoleto;
- uso de `UIWidget.Active` obsoleto;
- APIs antiguas de Database/Leaderboard;
- comparación de `entity<Entity>` con `EResItem` en `PlayerController.fcg:94`;
- sombras de variables en `GlobalLeaderboard.fcg`.

No hubo diagnósticos de tipo error.

### Símbolos generados

- Referencias `ERes*.<miembro>` distintas encontradas: 195.
- Confirmadas contra `EditorGenLib.fcc`/bibliotecas oficiales: 191.
- No confirmadas: 4, todas dentro de bloques comentados (`RoundStartBanner` y `TeamNames1/2/3`).
- Los símbolos de escena `StartPos1..11`, `EndPos1List..11` y `SpawnGroup1..11` existen en el archivo generado.

### Validación de activos del editor

- Craftland Studio MCP: no disponible en la sesión.
- `instructions://index`: no se pudo leer.
- Build del editor y consola: no ejecutados.
- `.eca`, escenas, UI, Workflow, ProjectSettings, attachments y registros: no verificados.

## Estado de entrega

- Auditoría estática: realizada con limitaciones documentadas.
- Compilación completa: aprobada con advertencias, código de salida `0`.
- Prueba en juego: no realizada.
- Cambios FC: corrección de estabilidad preparada en `fix/estabilidad-combate` y pendiente de revisión mediante Pull Request.
- Cambio de zona: no aplicado, porque aún no puede validarse con seguridad.

## Requisitos para cerrar la validación

1. Abrir y guardar el proyecto en Craftland Studio.
2. Conectar Craftland Studio MCP.
3. Verificar geometría, Workflow, ajustes, attachments y registro actual desde el editor.
4. Ejecutar `game-build` y revisar `game-console-get-logs` si se modifican activos o attachments.
