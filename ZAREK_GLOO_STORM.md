# ZAREK GLOO STORM

## Estado real

ZAREK GLOO STORM está integrado como un **overlay visual soportado** que acompaña a la pared Gloo oficial. No es una `WeaponSkinID` personalizada y no sustituye la entidad jugable de la pared.

- Recurso original: creado.
- Texturas y materiales: importados mediante Craftland Studio MCP.
- Prefab visual: creado, guardado y registrado mediante MCP.
- Integración FC: compilada y empaquetada.
- Prueba visible en cliente, colisión, destrucción y multicliente: pendiente.

## Investigación del mecanismo oficial

El SDK instalado define `IceWall` con estas propiedades relevantes:

- `MaxHP`;
- `LifeTime`;
- `MaxCount`;
- `AssetConfigID WeaponSkinID` con la etiqueta `Piel`;
- `OverloadByPlayerWeaponSkin Bool` con la etiqueta `Prioridad de Configuración del Jugador`.

El registro FC admite `weaponskin`, pero los 187 recursos encontrados por el MCP son elementos oficiales `lib://` y de solo lectura. El servidor no expone una herramienta para crear una `WeaponSkinID` a partir de PNG, material o prefab importado. Por tanto:

- sí se puede seleccionar una skin oficial existente;
- no se verificó una ruta oficial para publicar una skin Gloo importada;
- no se modificó el cliente ni se usaron reemplazos externos;
- no se declara ZAREK GLOO STORM como skin oficial personalizada.

El evento oficial `OnUseConsumable(consumableID, item, spawnedItem)` entrega la entidad creada por el consumible. La implementación filtra `EResItem.GlooWall`, valida `spawnedItem != nil`, crea el prefab visual registrado y lo parenta a la pared. `PlayerController.fcg` está adjunto a `ProjectSettings/PlayerEntity.asset`, por lo que el listener corresponde a cada jugador.

## Recursos registrados

| Recurso | Identificador de Craftland | Uso |
| --- | --- | --- |
| `Assets/ZarekGlooStorm/ZarekGlooStorm_Front.png` | `u5sm8gix73j-mwep5tsi-zowditp1g4` | cara frontal, 1774×887 |
| `Assets/ZarekGlooStorm/ZarekGlooStorm_Back.png` | `iqhv8a3hfxa-mwep5u77-epyt9dwhna7` | cara posterior, 1773×887 |
| `ZarekGlooStorm_Material_Front.mat` | `mx9wfd1vruf-mwep6jn9-hkywmribrju` | material frontal |
| `ZarekGlooStorm_Material_Back.mat` | `x8cqbyguu6c-mwep6jp2-l34pc1fvpp` | material posterior |
| `ZarekGlooStorm_Overlay.prefab` | `g42604hro3-mwep6rp0-spem5p30wbq` | overlay de dos caras |
| Registro FC | `EResPrefab.ZarekGlooStormOverlay` | creación desde FC |

Los dos materiales usan `BRMobile/UGC/AlphaBlendColor`, brillo `1.05`, una textura cada uno y ningún efecto dinámico. El prefab contiene dos `MeshEntity` con `MeshRender`; no contiene `Collider`, `ColliderShape` ni `Rigidbody`.

La importación conserva `maxSize = 2048` y mipmaps. Se solicitó `1024` mediante MCP, pero la lectura posterior siguió devolviendo `2048`; no se editó el `.meta` a mano. La compresión/resolución final debe comprobarse en un dispositivo móvil y, si el inspector oficial lo permite, ajustarse allí.

## Alcance visual

La composición es predominantemente azul (`#006BFF`, `#008CFF`, `#06132C`), con cian (`#00D9FF`) reservado para grietas y rayos. La Z central es el foco. No hay animales, caras, mascotas ni personajes.

Las fuentes publicables permanecen en `Design/ZarekGlooStorm/`:

- `ZarekGlooStorm_Front.png`;
- `ZarekGlooStorm_Back.png`;
- `ZarekGlooStorm_Logo.png`;
- `ZarekGlooStorm_Preview.png`.

El preview no se importó a `Assets` y no se usa como sustituto de una pared funcional.

## Comportamiento preservado

La implementación no escribe `MaxHP`, `LifeTime`, `MaxCount`, `AssetConfigID`, inventario ni propiedades de daño. Tampoco añade colisión. Se mantienen:

- 20 paredes Gloo entregadas por ronda;
- tres rondas;
- salud, resistencia y daño de la pared oficial;
- colocación, dimensiones jugables y destrucción oficiales;
- tienda, puntuación y flujo competitivo existentes.

El listener se ejecuta por cada consumo de Gloo, no solo para la primera pared; esto cubre las paredes iniciales y las obtenidas después. La uniformidad entre equipos y la replicación a otros clientes requieren prueba multicliente.

## Validación técnica

- `fccompile.exe -i Assets`: salida `0`, sin errores y con 67 advertencias heredadas.
- `game-build debug`: aprobado.
- `game-build release`: aprobado; generó `Temp/Build/FEUserLevelData.bytes`.
- Registro `ZarekGlooStormOverlay`: existe, no está obsoleto y apunta al prefab correcto.
- Consola: permanece un diagnóstico previo del `TextEntity` del monumento ZAREK; no se registraron errores nuevos del overlay durante build.

## Pruebas runtime pendientes

La automatización de Windows informó `native pipe unavailable`, y el MCP de Craftland no expone un comando de Play/Run. Por ello, estas pruebas no están aprobadas todavía:

1. Iniciar el cliente y desplegar al menos cinco paredes consecutivas.
2. Ver cada pared de frente, detrás y en ángulo; comprobar orientación y escala.
3. Caminar y disparar contra la pared para confirmar que solo colisiona la entidad oficial.
4. Destruir la pared y confirmar que el overlay desaparece con su padre.
5. Repetir con dos clientes y ambos equipos para validar replicación.
6. Repetir en los 11 escenarios y en las tres rondas.
7. Medir FPS y memoria en un dispositivo móvil; revisar transparencias y `maxSize`.
8. Confirmar que las 20 unidades de inventario y cualquier Gloo adquirida después usan el mismo aspecto.

Si el overlay no replica o no desaparece correctamente en runtime, debe retirarse o ajustarse; no se debe alterar la pared oficial para ocultar ese fallo.
