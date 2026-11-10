# Changelog — ZAREK ENDZONE PRO

Todos los cambios relevantes del proyecto se registran aquí. El proyecto aún no usa versionado semántico formal.

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
