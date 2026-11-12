# Diseño visual — ZAREK ENDZONE PRO

## Estado

La identidad ZAREK ya está integrada en recursos reales de Craftland Studio mediante su MCP oficial. El emblema canónico fue importado y registrado, los marcadores y el acceso a la tienda recibieron la paleta azul/cian, y el escenario contiene un letrero tridimensional `ZAREK ENDZONE PRO` en cian, sin colisión.

Las imágenes de concepto permanecen como referencias. No sustituyen widgets interactivos ni datos reales: la integración conserva los controles, callbacks, columnas y lógica existentes.

## Sistema visual

| Rol | Color | Uso propuesto |
| --- | --- | --- |
| Fondo principal | `#080D18` | paneles y superficies profundas |
| Fondo secundario | `#071B3A` | filas, contenedores y contraste |
| Azul ZAREK | `#008CFF` | selección, bordes y marca principal |
| Cian de energía | `#00D9FF` | vivos, acentos y luz secundaria |
| Blanco frío | `#F4FAFF` | datos y títulos |

La identidad usa una `Z` angular de lectura inmediata. El marcador prioriza datos competitivos y mantiene las tres filas que existen actualmente en los recursos UI registrados. No se alteran puntuación, rondas, equipos ni lógica de juego.

## Fuentes preparadas

| Archivo | Dimensiones | Propósito | Estado |
| --- | ---: | --- | --- |
| `Design/ZarekBrand/zarek-z-logo-source.png` | 1536×1024, ARGB | emblema Z con transparencia | fuente canónica importada y registrada como `ZarekLogo` |
| `Design/ZarekBrand/zarek-sky-sign-concept.png` | 1672×941 | referencia del letrero celeste `ZAREK` | concepto, no integrado |
| `Design/ZarekBrand/zarek-scoreboard-concept.png` | 1672×941 | referencia del marcador de tres filas | concepto, no integrado |
| `Design/ZarekBrand/zarek-final-screen-concept.png` | 1672×941 | referencia de pantalla de campeones | concepto, no integrado |
| `Design/ZarekBrand/zarek-armory-concept.png` | 1672×941 | referencia de tienda `ZAREK ARMORY` | concepto, no integrado |

Las cinco imágenes se generaron con la herramienta integrada de generación de imágenes. No se usaron imágenes de terceros como referencia. Solo el emblema canónico se convirtió en activo del juego; los otros cuatro archivos siguen siendo mockups de dirección visual.

## Cambios FC aplicados

En `GlobalUIManager.fcg`:

- la presentación inicial dura 3 segundos en vez de 8;
- la creación del banner espera 2 segundos después de `OnPlayerJoin` para no consumirse detrás de la pantalla de carga;
- la apertura usa el título `ZAREK ENDZONE PRO`, la descripción `DOMINA LA ÚLTIMA ZONA` y el sprite registrado `EResSprite.ZarekLogo`;
- la escuadra local usa azul eléctrico y cian en el minimarcador;
- la fila local de los marcadores BOOYAH y ESPORT cambia del dorado anterior a `#008CFF`;
- el tooltip del marcador ESPORT muestra `ZAREK ENDZONE PRO | RONDA n/3` usando un widget ya registrado;
- la pantalla final muestra `ZAREK ENDZONE PRO` y `DOMINA LA ÚLTIMA ZONA` sin sustituir el marcador ni su lógica.

Estos cambios reutilizan propiedades y widgets confirmados por los símbolos actuales. No crean asset IDs, widgets ni APIs nuevos.

## Integración realizada en Craftland Studio

- El MCP oficial respondió y se leyó `instructions://index` antes de modificar activos.
- `Assets/Resources/zarek-z-logo-source.png` fue importado y registrado como sprite `ZarekLogo`; el símbolo generado `EResSprite.ZarekLogo` fue verificado sin editar archivos generados.
- `MiniScoreBoard.ui`, `FullScoreBoard.ui` y `EsportFullScoreBoard.ui` conservan sus widgets y datos, con fondos `#080D18` / `#071B3A` y acentos `#008CFF` / `#00D9FF`.
- `ShopIcon.ui` conserva su único callback `OnPress`, usa el emblema registrado y muestra `ZAREK ARMORY`.
- `New Scene.scene` contiene una sola entidad de texto `ZAREK Sky Sign`, con contenido `ZAREK ENDZONE PRO`, color `#00D9FF`, profundidad `1.1`, ancho `42` y tamaño de fuente `150`.
- El rótulo permanece por encima del área jugable, sin collider ni obstrucción de spawns o zonas. El acabado cian fue inspeccionado visualmente y guardado en el editor.
- Una ejecución anterior cargó el HUD y las tres rondas; el intento posterior al acabado final quedó detenido al 0 %, por lo que esa comprobación runtime sigue pendiente.

`key.csv` conserva los textos originales porque el MCP disponible no expone edición segura de filas de localización. La apertura y la pantalla final usan textos directos confirmados por FC; no se modificó el CSV serializado a mano.

## Verificación visual pendiente

1. Capturar la apertura de 3 segundos y comprobar título, descripción y emblema en una ejecución nueva.
2. Abrir `ZAREK ARMORY`, comprobar el botón `OnPress` y validar el emblema en preparación.
3. Mostrar los marcadores completo y ESPORT en varias resoluciones y revisar clipping, contraste y encabezado.
4. Completar una partida de tres rondas para inspeccionar la pantalla final real.
5. Repetir la carga del cliente y confirmar desde la cámara del jugador la legibilidad y no obstrucción del letrero celeste.

## Criterios de aceptación runtime

- El letrero es visible desde la arena sin tapar enemigos, zona ni HUD.
- El emblema no se deforma y mantiene margen transparente.
- El marcador sigue mostrando correctamente posición, escuadra, Booyah, kills y puntos.
- `ZAREK ENDZONE PRO | RONDA n/3` no se recorta en resoluciones 16:9 y 16:10.
- La fila propia se distingue sin reducir la legibilidad del texto.
- La apertura desaparece a los 3 segundos y no bloquea controles.
- No aparecen errores nuevos de asset, script, attachment ni widget; el diagnóstico del editor `Find no ecaItem` asociado al preset `TextEntity` debe revisarse por separado.

## Prompts finales de las fuentes

### Emblema

`Monograma Z angular original para ZAREK ENDZONE PRO, vectorial, azul eléctrico y cian, silueta fuerte, fondo realmente transparente, sin armas, escudos, marcas ajenas ni watermark.`

### Letrero celeste

`Concepto 16:9 de arena competitiva al anochecer con un gran letrero flotante que diga exactamente ZAREK una sola vez, emblema Z integrado, geometría modular realizable y paleta azul/cian.`

### Marcador

`Mockup 16:9 de marcador premium ZAREK ENDZONE PRO con exactamente tres filas, RONDA 2/3 y columnas POS, ESCUADRA, BOOYAH, KILLS y PTS, alto contraste y paleta azul/cian.`

### Pantalla final

`Mockup 16:9 de pantalla final ZAREK ENDZONE PRO con CAMPEONES, EQUIPO 1, POSICIÓN #1, 32 PTS y 8 KILLS, diseño geométrico azul/cian y sin marcas ajenas.`

### ZAREK ARMORY

`Mockup 16:9 de tienda ZAREK ARMORY con pestañas ARMAS, EQUIPO y UTILIDAD, cuatro tarjetas, panel SELECCIONADO y botón COMPRAR, interfaz práctica azul/cian.`
