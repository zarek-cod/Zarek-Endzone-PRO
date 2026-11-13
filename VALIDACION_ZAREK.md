# Validación — ZAREK ENDZONE PRO

Última actualización: 2026-11-12.

## Entorno

- Sistema: Windows.
- Git: `2.55.0.windows.3`.
- GitHub CLI: `2.103.3` instalado durante esta sesión.
- Craftland Studio declarado por `main.gproj`: `1.19.4689915`.
- Compilador FC configurado: `C:\Program Files\Craftland Studio\resources\LocalData\Utilities\UGCLanguage\release\fccompile.exe`.
- Identificador de sesión de compilación: `2176be10-916b-4600-aa2f-e8e98ba1971e`.
- Modelo reportado al compilador: `unknown-model`, porque el runtime no expone un identificador de modelo exacto reutilizable.

## Validaciones ejecutadas

### Evolución visual y competitiva 2.0 — 2026-11-12

Cambios verificados:

- `GameInfo.ui` muestra una cabecera ZAREK persistente con emblema registrado, título, lema y paleta azul/cian; los widgets reales de ronda, tiempo y marcador siguen funcionando;
- `New Scene.scene` contiene `ZAREK Sky Monument` a `y = 130`, con tres líneas, billboard y sin collider;
- Workflow confirmado en el editor con `3` rondas y duraciones `Before Combat = 60`, `FirstShrinkPrep = 5`, `Combat = 65`, `MovingSafeZone = 65`, `LastSafeZone = 60`;
- `GlobalSafeZones.fcg` usa radios iniciales de 48–60 m, primer cierre de 28–33 m, segundo cierre de 20–25 m y cierre final a 0 m, todos con 60 segundos efectivos;
- se añadieron guardas para mapa de zona incompleto, lista de puntos finales vacía, endpoint `nil` y lista de escenarios vacía;
- el temporizador persistente se inicia también durante los 60 segundos de preparación;
- las tres rondas eligen escenarios distintos; se conserva el fallback determinista si el generador aleatorio repite índices;
- se eliminó la llamada runtime a `PlayersTimerUI`, grafo obsoleto no montado que generaba `No hay script ... PlayersTimerUI ... en Entidad`;
- se conservaron `esportMaxRound = 3` y `glooWallsPerRound = 20`; no se cambió puntuación, persistencia ni lógica de final de combate del PR #1.

Matriz de radios validada estáticamente:

| Escenario | Inicial | 1.er cierre | Tras movimiento | Final | Duración por cierre |
| --- | ---: | ---: | ---: | ---: | ---: |
| STAGE_1 | 48 | 28 | 20 | 0 | 60 s |
| STAGE_2 | 50 | 29 | 21 | 0 | 60 s |
| STAGE_3 | 58 | 33 | 25 | 0 | 60 s |
| STAGE_4 | 55 | 32 | 24 | 0 | 60 s |
| STAGE_5 | 54 | 31 | 23 | 0 | 60 s |
| STAGE_6 | 56 | 32 | 24 | 0 | 60 s |
| STAGE_7 | 60 | 33 | 25 | 0 | 60 s |
| STAGE_8 | 55 | 31 | 23 | 0 | 60 s |
| STAGE_9 | 48 | 28 | 20 | 0 | 60 s |
| STAGE_10 | 48 | 28 | 20 | 0 | 60 s |
| STAGE_11 | 52 | 30 | 22 | 0 | 60 s |

Validación técnica final:

```text
fccompile.exe -i Assets -agent unknown-model -session 2176be10-916b-4600-aa2f-e8e98ba1971e
```

- código de salida: `0`;
- errores FC: `0`;
- advertencias: `67`, heredadas de las mismas familias ya registradas;
- `git diff --check`: aprobado;
- `game-build` debug y build release final: aprobados, con `Temp/Build/FEUserLevelData.bytes` generado;
- prueba runtime: el cliente superó 0 %, entró al mapa y mostró dos escenarios seleccionados en ejecuciones independientes (`Torre del Reloj` y `Ciudad del Cabo`), `Ronda 1–3`, cabecera ZAREK, marcador funcional y tienda real abierta;
- la segunda ejecución ya no presentó el error del script obsoleto `PlayersTimerUI`.

Estado por categoría:

- **Implementado y probado:** carga del cliente, HUD ZAREK persistente, marcador real, contador de tres rondas, nombre de escenario y apertura de la tienda real.
- **Implementado, pendiente de prueba completa:** temporizador de preparación, progresión temporal de los tres cierres, no repetición a lo largo de las tres rondas, letrero visto desde la cámara jugable, apertura de 3 segundos y pantalla final.
- **Recurso creado, no integrado:** los cuatro mockups de cielo, marcador, pantalla final y armory continúan solo como referencias; la UI interactiva usa recursos reales.
- **Diagnóstico pendiente:** el preset oficial `TextEntity` sigue emitiendo `Find no ecaItem for id:-125000` y el cliente registra `No hay propiedad -97000 en Entidad` al cargar; no impiden entrar al mapa ni usar HUD/tienda.

Pruebas Craftland pendientes:

1. Jugar las tres rondas y cronometrar cada transición de zona hasta 0 m.
2. Confirmar visualmente tres escenarios distintos en una misma partida.
3. Ver el monumento celeste desde cada escenario y revisar oclusión/distancia.
4. Capturar la apertura de 3 segundos y la pantalla final real.
5. Repetir con varios clientes y resoluciones 16:9/16:10.

### Integración visual ZAREK — 2026-11-11

Craftland Studio MCP:

- conexión real confirmada mediante una lectura de solo lectura de `instructions://index`;
- proyecto correcto abierto en Craftland Studio;
- emblema importado a `Assets/Resources/zarek-z-logo-source.png` y registrado oficialmente como sprite `ZarekLogo`;
- símbolos generados `EResSprite.ZarekLogo` y `EResKeySprite.ZarekLogo` comprobados como referencias de solo lectura;
- activos `.ui` y escena guardados exclusivamente mediante herramientas oficiales del editor.

Activos integrados:

- apertura: espera inicial de 2 segundos y luego `ZAREK ENDZONE PRO`, `DOMINA LA ÚLTIMA ZONA` y `EResSprite.ZarekLogo` durante 3 segundos;
- marcadores `MiniScoreBoard.ui`, `FullScoreBoard.ui` y `EsportFullScoreBoard.ui`: 43 sustituciones de color azul/cian, sin eliminar widgets, columnas ni bindings;
- `ShopIcon.ui`: sprite `ZarekLogo`, estados azul/cian, texto visible `ZAREK ARMORY` y un único handler `OnPress` conservado;
- `New Scene.scene`: una sola entidad `ZAREK Sky Sign`, texto `ZAREK ENDZONE PRO`, color `#00D9FF`, profundidad `1.1`, ancho `42`, a más de 30 metros sobre el límite vertical medido y sin componentes de colisión;
- pantalla final: título y subtítulo ZAREK asignados a los campos existentes del HUD.

Validación técnica:

```text
fccompile.exe -i Assets -agent unknown-model -session 2176be10-916b-4600-aa2f-e8e98ba1971e
```

- código de salida: `0`;
- errores: `0`;
- advertencias: `67`, de familias heredadas (APIs obsoletas, sombras en `GlobalLeaderboard` y comparación `entity<Entity>`/`EResItem`);
- `game-build` release final: completado correctamente; salida generada en `Temp/Build/FEUserLevelData.bytes`;
- consola posterior: un diagnóstico de sistema no fatal del editor para el preset oficial `TextEntity` (`Find no ecaItem for id:-125000`); no hay error FC ni fallo de empaquetado;
- cliente de prueba anterior: cargó el escenario y mostró HUD operativo con `Ronda 0 - 3`;
- último intento tras el acabado final: detenido en la carga al 0 %, por lo que no se considera una prueba runtime aprobada.

Estado de las pruebas visuales:

- **implementado y comprobado en editor:** registro del logo, paleta de los activos UI y rótulo cian `ZAREK ENDZONE PRO` visible sin colisión;
- **implementado y comprobado técnicamente:** compilación FC y build release;
- **comprobado en una ejecución anterior:** carga del cliente y HUD de tres rondas;
- **implementado, pendiente de comprobación visual runtime final:** apertura, letrero actualizado, pantalla final, botón de tienda y marcadores completo/ESPORT;
- **recurso creado, no integrado:** los cuatro mockups de cielo, marcador, pantalla final y armory siguen siendo referencias conceptuales;
- **limitación:** `key.csv` no se editó porque el MCP disponible no ofrece una operación segura de edición de filas; se usaron textos directos en los widgets existentes.

### Registro histórico — inicio de la rama `feature/zarek-visual-premium`

Esta subsección conserva el estado de la primera etapa, antes de habilitar el MCP; sus pendientes quedaron superados por la integración del 2026-11-11 descrita arriba.

Archivos de código modificados:

- `Assets/Scripts/Global/GlobalManager/GlobalUIManager.fcg`.

Fuentes de diseño añadidas fuera de `Assets/`:

- `Design/ZarekBrand/zarek-z-logo-source.png`;
- `Design/ZarekBrand/zarek-sky-sign-concept.png`;
- `Design/ZarekBrand/zarek-scoreboard-concept.png`;
- `Design/ZarekBrand/zarek-final-screen-concept.png`;
- `Design/ZarekBrand/zarek-armory-concept.png`;
- `DISENO_ZAREK.md`.

Comprobaciones:

- se reutilizan únicamente `UIOpeningBanner`, `UIWidgetImage`, `UIWidgetLabel`, `TooltipText` y propiedades ya presentes en el proyecto;
- no se inventaron asset IDs, widgets, componentes ni APIs;
- no se cambió puntuación, Workflow, rondas, paredes gloo, zonas ni cantidad de equipos;
- ninguna imagen se copió a `Assets/` ni se registró manualmente;
- en esa primera etapa, Craftland Studio estaba abierto con este `main.gproj`, pero su MCP aún no estaba expuesto y no se pudo leer `instructions://index`.

Compilación:

```text
"C:\Program Files\Craftland Studio\resources\LocalData\Utilities\UGCLanguage\release\fccompile.exe" -i Assets
```

Resultado:

- código de salida: `0`;
- errores: ninguno;
- advertencias: 67 encabezados de las familias heredadas (`ClearResidualLevelObjects()`, `UIWidget.Active`, Database/Leaderboard, comparación `entity<Entity>`/`EResItem` y sombreado de variables);
- diagnósticos nuevos en `GlobalUIManager.fcg`: ninguno.

Pendiente documentado en esa primera etapa:

1. Registrar el emblema e integrarlo a las interfaces.
2. Construir y colocar el letrero celeste sin colisión ni interferencia jugable.
3. Verificar clipping del tooltip, colores, contraste y apertura de 3 segundos.
4. Integrar pantalla final y ZAREK ARMORY mediante recursos del editor.
5. Ejecutar build, consola y pruebas runtime; no se declaran aprobadas en esta sesión.

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

- Craftland Studio MCP: disponible y con respuesta oficial comprobada.
- `instructions://index`: leído antes de las operaciones de editor.
- Build del editor: aprobado en modo release.
- Consola posterior al build: diagnóstico no fatal `Find no ecaItem` del preset `TextEntity`; sin error FC ni fallo de empaquetado.
- Escena, recursos UI y registro de sprite: modificados y consultados mediante el MCP; no se editaron formatos serializados a mano.

## Estado de entrega

- Auditoría estática y de recursos: realizada.
- Compilación completa: aprobada con 67 advertencias heredadas, código de salida `0`.
- Build release: aprobado.
- Prueba de carga en juego: realizada; HUD básico y tres rondas visibles.
- Flujo completo de partida y pantallas temporales/finales: pendiente, sin marcar como aprobado.
- Rondas, 20 paredes gloo, zonas, puntuación y cantidad de equipos: sin cambios.

## Requisitos para cerrar la validación

1. Reiniciar una ejecución y capturar la apertura antes de que expire a los 3 segundos.
2. Probar visualmente el marcador completo/ESPORT y abrir `ZAREK ARMORY`.
3. Completar tres rondas para validar la pantalla final ZAREK.
4. Comprobar el letrero desde la cámara jugable y repetir con varios clientes/resoluciones.
