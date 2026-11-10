# Diseño visual — ZAREK ENDZONE PRO

## Estado

Esta rama contiene una primera capa segura de branding en FC y cinco fuentes visuales originales. Las imágenes están deliberadamente fuera de `Assets/`: son referencias de diseño y **todavía no están registradas ni visibles dentro del juego**.

Craftland Studio está abierto con este proyecto, pero su servidor MCP no está expuesto a la sesión. Por esa razón no se editaron `.ui`, escenas, prefabs, localización, Workflow, attachments ni otros recursos serializados.

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
| `Design/ZarekBrand/zarek-z-logo-source.png` | 1536×1024, ARGB | emblema Z con transparencia | fuente sin registrar |
| `Design/ZarekBrand/zarek-sky-sign-concept.png` | 1672×941 | referencia del letrero celeste `ZAREK` | concepto, no integrado |
| `Design/ZarekBrand/zarek-scoreboard-concept.png` | 1672×941 | referencia del marcador de tres filas | concepto, no integrado |
| `Design/ZarekBrand/zarek-final-screen-concept.png` | 1672×941 | referencia de pantalla de campeones | concepto, no integrado |
| `Design/ZarekBrand/zarek-armory-concept.png` | 1672×941 | referencia de tienda `ZAREK ARMORY` | concepto, no integrado |

Las cinco imágenes se generaron con la herramienta integrada de generación de imágenes. No se usaron imágenes de terceros como referencia. Deben pasar por revisión visual y registro oficial en Craftland Studio antes de convertirse en activos del juego.

Los mockups exploran variantes del símbolo Z. Antes de registrar recursos debe elegirse un único emblema canónico. Para UI pequeña también se necesita una exportación plana, compacta y con menos halo que la fuente actual.

## Cambios FC aplicados

En `GlobalUIManager.fcg`:

- la presentación inicial dura 3 segundos en vez de 8;
- la escuadra local usa azul eléctrico y cian en el minimarcador;
- la fila local de los marcadores BOOYAH y ESPORT cambia del dorado anterior a `#008CFF`;
- el tooltip del marcador ESPORT muestra `ZAREK ENDZONE PRO | RONDA n/3` usando un widget ya registrado.

Estos cambios reutilizan propiedades y widgets confirmados por los símbolos actuales. No crean asset IDs, widgets ni APIs nuevos.

## Integración pendiente en Craftland Studio

Cuando MCP esté disponible, el orden seguro es:

1. Leer `instructions://index`.
2. Revisar Workflow, attachments, recursos UI y configuración de equipos.
3. Importar y registrar el emblema con las herramientas oficiales de asset registration.
4. Añadir en el editor un header y un widget de logo a `MiniScoreBoardUI`, `FullScoreBoard` y `EsportFullScoreBoard`, conservando todas las columnas existentes.
5. Crear el letrero `ZAREK` como entidad de escena sin colisión, lejos de spawns y trayectorias, y validar legibilidad desde cada zona activa.
6. Aplicar marca a la pantalla final y a la tienda solo mediante sus recursos registrados; no reemplazar archivos `.ui` o localización manualmente.
7. Ejecutar build del editor, revisar consola y probar rendimiento, clipping, escalado y contraste.

## Criterios de aceptación runtime

- El letrero es visible desde la arena sin tapar enemigos, zona ni HUD.
- El emblema no se deforma y mantiene margen transparente.
- El marcador sigue mostrando correctamente posición, escuadra, Booyah, kills y puntos.
- `ZAREK ENDZONE PRO | RONDA n/3` no se recorta en resoluciones 16:9 y 16:10.
- La fila propia se distingue sin reducir la legibilidad del texto.
- La apertura desaparece a los 3 segundos y no bloquea controles.
- No aparecen errores de asset, script, attachment ni widget en consola.

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
