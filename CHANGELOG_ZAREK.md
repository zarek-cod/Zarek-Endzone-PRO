# Changelog — ZAREK ENDZONE PRO

Todos los cambios relevantes del proyecto se registran aquí. El proyecto aún no usa versionado semántico formal.

## 2026-11-10 — Validación final del cierre de combate del PR #1

### Corregido

- `OnPlayerQuit` conserva la fase en la que ocurrió la salida y ya no puede resolver una ronda por un abandono iniciado fuera del combate.
- La lectura de equipos tras una desconexión se difiere dos frames: primero se aplica `EliminateAtNextFrame` y después se permite que se estabilicen `Team.IsAced` y `OnACE`.
- La resolución de ronda queda limitada a `FirstShrinkPrep`, `Combat`, `MovingSafeZone`, `LastSafeZone` y `SuddenDeath`; `Preparation`, `__Before6Combat__` y las fases de cierre quedan excluidas.
- Los eventos `OnACE` del mismo frame se agrupan en una única comprobación diferida e idempotente, evitando que una eliminación simultánea declare vencedor antes de registrar el último equipo eliminado.
- `teamRanking` ya no acepta equipos duplicados ni eliminaciones producidas fuera de las fases activas.
- La ruta sin supervivientes marca la ronda como terminada durante su procesamiento, sin cambiar la puntuación, persistencia ni presentación sin ganador ya aprobadas.
- La animación de posición del marcador ESPORT usa ahora los mismos puntos competitivos que la puntuación real (`12/9/8/7/6/5/4`) en vez de mostrar el índice de eliminación.

### Conservado

- El atajo de última escuadra conectada continúa limitado a BOOYAH_COUNT y ahora comparte el mismo cierre idempotente.
- Se mantienen 3 rondas, 20 paredes gloo y la lógica existente de zonas.
- No se modificaron escenas, Workflow, attachments, recursos binarios ni configuraciones del editor.

### Validación

- `git diff --check`: aprobado.
- `fccompile.exe -i Assets`: código de salida `0`, sin errores y 66 advertencias heredadas.
- Craftland Studio estaba abierto con este proyecto, pero su servidor MCP no estuvo expuesto a la sesión: Workflow, attachments, configuración de equipos y pruebas runtime permanecen pendientes y no se marcaron como aprobados.

## 2026-11-10 — Correcciones de revisión del PR #1

### Corregido

- Las rondas sin supervivientes actualizan el marcador mediante una ruta explícita sin ganador; no se concede ni anima Booyah.
- En ESPORT, el campeón general se determina siempre por la clasificación acumulada aunque solo quede una escuadra conectada.
- La salida de un jugador ya no finaliza anticipadamente una partida ESPORT cuando queda una sola escuadra conectada; el flujo continúa como cierre de ronda.
- El desempate ESPORT ya no favorece equipos conectados y aplica, en orden: puntos totales, Booyahs, kills y puntos de posición.
- Solo se declaran varios campeones cuando coinciden exactamente los cuatro valores anteriores.
- `GlobalCurrencyManager.CombatEnd` omite todas las recompensas cuando `winningTeam` es `nil`.
- La persistencia final de kills, muertes, partida y resultado se centralizó y cuenta con protección contra escrituras duplicadas.
- La ruta final sin ganador también persiste el resultado usando los campeones de la clasificación acumulada.

### Conservado

- Modo BOOYAH_COUNT mantiene su tratamiento especial cuando solo existe una escuadra activa.
- `esportMaxRound = 3` y `glooWallsPerRound = 20`.
- Tienda, cantidad de escuadras y lógica de zonas sin cambios en esta revisión.
- Sin modificaciones deliberadas de escenas, recursos binarios, attachments o configuraciones del editor.

### Validación

- `git diff --check`: aprobado.
- Compilación FC completa: código de salida `0`, sin errores y con las advertencias heredadas ya documentadas.
- No se pudo verificar el attachment de `GlobalCurrencyManager` ni ejecutar pruebas runtime porque Craftland Studio MCP no está disponible.

## 2026-11-09 — Estabilidad de cierre de combate

### Corregido

- `CombatEnd` termina inmediatamente después de detectar un `winningTeam` nulo y ya no continúa desreferenciándolo.
- `NoTeamWinException` dejó de seleccionar `GetAllTeams()[0]` y de conceder Booyah a un equipo arbitrario.
- Las bajas y los puntos de posición de los equipos realmente eliminados se conservan mediante `teamRanking`; una ronda sin ganador no añade Booyah.
- Al terminar la tercera ronda sin ganador de ronda, la clasificación acumulada determina los ganadores generales sin indexar listas vacías.
- `shopIconSize` se inicializa al comenzar la partida y también cuenta con inicialización defensiva antes de escribir dimensiones.
- Los dos accesos `GetAllShops()[0]` ahora verifican que exista al menos una tienda antes de crear la UI.

### Conservado

- `esportMaxRound = 3`.
- `glooWallsPerRound = 20`.
- Lógica, radios, tiempos y daño de zonas sin cambios.
- Recursos binarios y configuraciones del editor sin modificaciones deliberadas.

### Validación

- `fccompile.exe -i Assets -agent unknown-model -session 2176be10-916b-4600-aa2f-e8e98ba1971e`: código de salida `0`.
- Sin errores de compilación; permanecen advertencias heredadas detalladas en `VALIDACION_ZAREK.md`.
- Build y prueba runtime de Craftland pendientes porque Craftland Studio MCP no está conectado en esta sesión.

## 2026-11-09 — Auditoría y preparación de repositorio

### Añadido

- Reglas de exclusión para temporales de Craftland, endpoints MCP locales y contenido sin procedencia publicable confirmada.
- Atributos de Git para scripts, documentación y activos binarios.
- `AUDITORIA_ZAREK.md` con inventario, arquitectura, discrepancias y riesgos priorizados.
- `VALIDACION_ZAREK.md` con resultados reproducibles de validación.

### Modificado

- `README.md` incluye una advertencia de estado auditado y enlaza al informe actual.

### Jugabilidad

- Sin cambios de lógica FC ni activos del editor en esta entrada.

### Pendiente

- Verificación de activos mediante Craftland Studio MCP.
- Primer cambio controlado de zona, solo después de validar geometría y flujo.

### Validación

- Compilación FC completa aprobada con advertencias y código de salida 0 después de regenerar `editorGen`.
