# Validación — ZAREK ENDZONE PRO

Última actualización: 2026-11-09.

## Entorno

- Sistema: Windows.
- Git: `2.55.0.windows.3`.
- GitHub CLI: `2.103.3` instalado durante esta sesión.
- Craftland Studio declarado por `main.gproj`: `1.19.4689915`.
- Compilador FC configurado: `C:\Program Files\Craftland Studio\resources\LocalData\Utilities\UGCLanguage\release\fccompile.exe`.
- Identificador de sesión de compilación: `2176be10-916b-4600-aa2f-e8e98ba1971e`.
- Modelo reportado al compilador: `unknown-model`, porque el runtime no expone un identificador de modelo exacto reutilizable.

## Validaciones ejecutadas

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
