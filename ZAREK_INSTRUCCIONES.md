# ZAREK ENDZONE PRO — v0.1 experimental

**Estado: código modificado; revisión estática satisfactoria; aún sin compilación ni prueba en Craftland Studio.**

Esta versión se basa en tu proyecto original **Zarek Endzone Base**. No modifica assets `.asset`, `.ui`, `.scene`, `.eca`, `.meta` ni la configuración serializada del editor: esos elementos deben editarse dentro de Craftland Studio.

## Cambios incluidos en scripts

- `Assets/Scripts/Global/GlobalConfig.fcg`: 16 rondas, 20 paredes gloo, 2 granadas normales, 3 botiquines, 2 inhaladores, preparación de 30/25 segundos.
- `Assets/Scripts/Global/GlobalManager/GlobalPreparationPhaseManager.fcg`: aplica las cantidades de `GlobalConfig` y reparte los equipos activos entre las posiciones encontradas; avisa si faltan posiciones.
- `Assets/Scripts/Global/GlobalData/SpawnPoints.fcg`: patrones de aparición circulares adaptables al número de posiciones disponibles; antes tenían 3 índices fijos.
- `Assets/Scripts/Global/GlobalManager/GlobalCombatManager.fcg`: evita lista fija de tres equipos vivos y cuenta escuadras participantes con jugadores conectados.
- `Assets/Scripts/Global/GlobalManager/GlobalGameManager.fcg`: puntuación competitiva de 12/9/8/7/6/5/4 más una unidad por eliminación (ya existente). Protección cuando todos abandonan la sala.
- `Assets/Scripts/Global/GlobalManager/GlobalUIManager.fcg`: marcador integrado con rangos derivados del número de equipos; el panel personalizado original de tres equipos continúa limitado a tres filas y no intenta acceder a filas inexistentes. La tabla final admite nombres genéricos para escuadras adicionales.

## Cómo abrirlo (Windows)

1. **Guarda y cierra Craftland Studio**. Haz una copia de tu proyecto original; no sobrescribas su carpeta.
2. Extrae el ZIP completo en una carpeta nueva, por ejemplo `C:\Users\joels\Desktop\Zarek Endzone PRO v0.1`.
3. Abre Craftland Studio, selecciona **Abrir proyecto** y elige el `main.gproj` de la nueva carpeta, o la carpeta del proyecto si el editor lo solicita.
4. Usa la compilación/prueba propia del editor y revisa la consola; si aparecen errores, copia todo el mensaje y la ubicación del script.
5. **Primero prueba con los tres equipos originales**. El script de rondas indica 16, pero Garena advierte que esta plantilla tiene soporte recomendado de hasta 7: para una prueba controlada puedes cambiar `esportMaxRound int = 7` en `GlobalConfig.fcg` si falla la ronda 8 o siguientes.
6. Cuando confirme que compila, en **Módulo → Equipo**, puedes reducir temporalmente `Mínimo de jugadores` de 9 a 2 para probar con dos personas, dejando inicialmente 3 equipos y 4 integrantes. Esta configuración del editor **no ha sido modificada en el ZIP**.

## Lo que todavía NO está terminado

- **Siete escuadras de cuatro:** en los ajustes del editor aún están `TeamCount=3`, `MemberCount=4` y `MinNumOfMatchStart=9`. No modifiques todavía `TeamCount` a 7 en esta versión.
- **Apariciones:** cada uno de los 11 escenarios contiene solo tres posiciones por grupo. Para 7 equipos hacen falta **al menos siete posiciones válidas por escenario**, con ubicaciones seguras, capacidad de jugadores suficiente y sincronización con la zona segura. Añádelas mediante Craftland Studio, no editando `.scene` a mano.
- **Marcador de siete escuadras:** `MiniScoreBoardUI` y `EsportFullScoreBoard` todavía tienen solo tres filas; el código previene ciertos errores pero no sustituye la interfaz faltante. Crear/registrar una nueva interfaz es trabajo dentro de Craftland Studio.
- **ZAREK ARMORY:** el registro actual de activos solo incluye algunas armas (M590, WOODPECKER, AK47, XM8, SCAR, M4A1, AUG, FAMAS, GROZAX y más). **AWM, MP40, M1887, M82B, etc. aún no están registrados en `EResItem`**; se deben agregar y registrar desde el editor. El archivo `ZAREK_CATALOGO_TIENDA.csv` es un plan, no una tienda conectada.
- **Humo y firma Zarek en cielo:** recursos visuales y de escena aún pendientes.
- **Optimización:** probar FPS y límites de paredes con numerosos jugadores.

## Validación efectuada

- 35 archivos `.fcg` localizados.
- Estructura básica de corchetes/llaves verificada, ignorando comentarios y cadenas.
- Importaciones relativas `.fcg` comprobadas.
- Presencia de configuración y métodos cambiados verificada.
- **Compilador real: NO EJECUTADO.** La ruta de `fccompile.exe` del proyecto apunta a `C:\Program Files\Craftland Studio\resources\LocalData\Utilities\UGCLanguage\release\fccompile.exe`, que no existe en este entorno Linux. No hay Craftland Studio MCP conectado aquí. Esto no garantiza ausencia de errores de compilación ni integración.

## Siguiente fase de desarrollo

1. Compilación y primera partida de prueba.
2. Revisión de errores de consola.
3. Registro de armas y creación de la tienda gratuita completa.
4. Colocación de posiciones seguras y UI para 7 escuadras mediante el editor.
5. Confirmación de capacidad real de hasta 28 jugadores.
6. Integración de identidad Zarek y optimización.

Referencia: https://ffcraftland.garena.com/en/tutorial/cl/4-96
