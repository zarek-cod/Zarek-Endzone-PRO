# Changelog — ZAREK ENDZONE PRO

Todos los cambios relevantes del proyecto se registran aquí. El proyecto aún no usa versionado semántico formal.

## 2026-11-10 — Diseño visual premium ZAREK

### Añadido

- `DISENO_ZAREK.md` con identidad visual, plan de integración y pruebas de aceptación.
- Fuentes originales para emblema Z, letrero celeste, marcador, pantalla final y ZAREK ARMORY en `Design/ZarekBrand/`, fuera de los activos del editor.

### Modificado

- La presentación inicial se reduce de 8 a 3 segundos.
- Minimarcador y filas propias usan la paleta `#008CFF` / `#00D9FF` en lugar del dorado anterior.
- El tooltip ESPORT muestra la marca `ZAREK ENDZONE PRO` junto con la ronda actual.

### Limitaciones

- Las imágenes son conceptos no registrados; no se afirma que estén integradas en el juego.
- Sin cambios directos de `.ui`, escenas, prefabs, localización, Workflow, attachments o configuraciones del editor.
- Letrero celeste, logo real en interfaces, pantalla final y ZAREK ARMORY requieren Craftland Studio MCP.

### Validación

- `fccompile.exe -i Assets`: código de salida `0`, sin errores y 67 advertencias heredadas.
- Pruebas visuales/runtime pendientes porque el MCP no está expuesto a esta sesión.

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
