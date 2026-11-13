# Changelog — ZAREK ENDZONE PRO

Todos los cambios relevantes del proyecto se registran aquí. El proyecto aún no usa versionado semántico formal.

## 2026-11-12 — Evolución visual y competitiva 2.0

### Añadido

- Cabecera persistente ZAREK en `GameInfo.ui` con emblema registrado, placa oscura, acento cian, título y lema.
- Estados de zona en el HUD real: preparada, contrayendo, en movimiento, cierre final y muerte súbita.
- Temporizador visible también durante los 60 segundos de preparación.
- Tabla competitiva de radios para los 11 escenarios.
- Guardas de seguridad para datos de escenario ausentes, listas de endpoints vacías, endpoints `nil` y selección de escenario no inicializada.

### Modificado

- El monumento celeste ahora usa tres líneas (`ZAREK`, `ENDZONE PRO`, `DOMINA LA ULTIMA ZONA`), tamaño más legible y posición elevada sin colisión.
- Preparación sincronizada a 60 s en FC y Workflow.
- Contracción, movimiento y cierre final sincronizados a 60 s efectivos; `Combat` y `MovingSafeZone` conservan 5 s de margen de fase.
- Radios iniciales ajustados a 48–60 m, primer cierre a 28–33 m y segundo cierre a 20–25 m.
- Las tres rondas seleccionan escenarios distintos, con fallback determinista.
- Retirada la invocación al grafo obsoleto y no montado `PlayersTimerUI`.

### Conservado

- Tres rondas y 20 paredes gloo.
- Puntuación, persistencia, tienda interactiva, cantidad actual de equipos y protecciones de combate del PR #1.

### Validación

- `fccompile.exe -i Assets`: salida `0`, 0 errores y 67 advertencias heredadas.
- `git diff --check`: aprobado.
- `game-build` debug y build release final: aprobados; `FEUserLevelData.bytes` generado.
- Dos cargas reales superaron el antiguo bloqueo del 0 % y entraron al mapa.
- HUD ZAREK, marcador, `Ronda 1–3`, escenario y tienda real comprobados visualmente.
- Eliminado el error runtime de `PlayersTimerUI`; permanece el diagnóstico no bloqueante del preset oficial `TextEntity` y el mensaje asociado de propiedad desconocida al cargar.

### Pendiente

- Partida completa de tres rondas, cronometraje de las zonas, pantalla final y validación multicliente/resolución.
- Inspección del monumento celeste desde la cámara del jugador en los 11 escenarios.

## 2026-11-11 — Integración real de identidad ZAREK

### Añadido

- Emblema canónico importado a `Assets/Resources/zarek-z-logo-source.png` y registrado oficialmente como sprite `ZarekLogo`.
- Entidad tridimensional `ZAREK Sky Sign` en `New Scene.scene`, elevada sobre el área jugable y sin colisión.
- Marca visible `ZAREK ARMORY` en el acceso existente a la tienda.

### Modificado

- La apertura espera 2 segundos a que el cliente termine de cargar y luego muestra `ZAREK ENDZONE PRO`, `DOMINA LA ÚLTIMA ZONA` y el emblema registrado durante 3 segundos.
- `MiniScoreBoard.ui`, `FullScoreBoard.ui` y `EsportFullScoreBoard.ui` aplican fondos oscuros y acentos azul/cian sin sustituir datos, widgets ni lógica.
- `ShopIcon.ui` usa el emblema ZAREK, estados azul/cian y conserva su callback `OnPress`.
- La pantalla final usa los campos existentes para presentar el nombre y lema ZAREK.
- El rótulo celeste ahora presenta `ZAREK ENDZONE PRO` en `#00D9FF`, con mayor ancho y profundidad 3D; se descartaron capas de texto duplicadas para conservar una sola entidad estable.

### Conservado

- Tres rondas, 20 paredes gloo, zonas, puntuación, equipos y flujo competitivo.
- Los cuatro mockups restantes continúan como referencias y no sustituyen interfaces interactivas.

### Validación

- Craftland Studio MCP respondió a `instructions://index`; importación, registro, UI y escena se realizaron con sus herramientas oficiales.
- `fccompile.exe -i Assets`: código de salida `0`, sin errores y 67 advertencias heredadas.
- `game-build` release final: completado correctamente y con salida generada.
- La consola final contiene un diagnóstico no fatal del editor para el preset oficial `TextEntity` (`Find no ecaItem`); la compilación y el empaquetado terminan correctamente.
- Una ejecución previa cargó el HUD operativo con `Ronda 0 - 3`; el acabado cian final del letrero se inspeccionó visualmente y se guardó en Craftland Studio.

### Pendiente

- Verificación visual runtime de apertura, tienda, marcadores completo/ESPORT y pantalla final tras tres rondas.
- Validación del letrero desde la cámara de jugador y en varias resoluciones/clientes.
- Repetir la carga runtime del acabado final: el último intento quedó detenido al 0 % y no se marca como aprobado.
- `key.csv` conserva sus textos originales porque el MCP disponible no expone edición segura de filas; no se editó el CSV serializado a mano.

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
