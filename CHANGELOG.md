# Changelog

Todos los cambios relevantes del modpack se anotan aquí. Formato basado en [Keep a Changelog](https://keepachangelog.com/es-ES/1.1.0/).

## [1.0.0] - 2026-10-11

Primera versión del pack gestionado con packwiz. Sustituye el sistema anterior, en el que los mods y configs se pasaban a mano por mensajería.

### Añadido

- Modpack inicial para Minecraft 1.21.1 con NeoForge 21.1.248.
- 177 mods registrados con packwiz (metadatos de CurseForge y Modrinth) y 4 parches propios como jars locales: `apotheosis_less_towers_fix`, `matias_ae2_less_meteorites_fix`, `matias_less_mowzie_structures_fix` y `picturecam-leather-fix`.
- 246 archivos de configuración copiados de la carpeta `config/` del servidor, que es la fuente de verdad.
- Asignación de lado por mod:
  - Solo cliente: Sodium, Iris, Iris Shader Folder, NeoContinuity, Entity Culling, Entity Texture Features, BetterF3, AmbientSounds, Cubes Without Borders, Sound Physics Remastered y Storage Drawers Visual Tweaks.
  - Solo servidor: VoxelDash (antes se copiaba a mano; ahora se instala desde Modrinth).
- `.packwizignore` para excluir configs de cliente, Distant Horizons, `fml.toml`, `ferritecore-mixin.toml`, JEI, Spark, bases de datos, backups y archivos temporales. Cada jugador conserva sus ajustes gráficos y de rendimiento.
- `.gitattributes` con `* -text` para que git no altere los saltos de línea y los hashes del índice sigan siendo válidos.
- Sincronización automática del servidor (paso previo al arranque) y de Prism (comando de pre-lanzamiento).

### Cambiado

Versiones que packwiz tomó más recientes que las que había instaladas:

| Mod | Antes | Ahora |
|---|---|---|
| Apothic Attributes | 2.10.1 | 2.11.0 |
| Apothic Enchanting | 1.6.1 | 1.6.4 |
| Serene Seasons | 10.1.0.3 | 10.1.0.9 |
| Serene Seasons Plus | 5.1.1 | 5.2.1 |
| Placebo | 9.9.2 | 9.9.3 |
| Create: Pipes'n Physics | 3.1.0 | 3.2.1 |
| Distant Horizons | 3.2.0-b (servidor) | 3.3.3 |

Distant Horizons estaba en versiones distintas en servidor y cliente; ahora ambos usan la 3.3.3.

### Decisiones

- Separate Sable Render Distance (SSRD) queda como `both`, porque Modrinth lo declara necesario en el servidor.
- Se rechazaron las dependencias opcionales Better Days (Serene Seasons Plus) y Sodium/Sable (SSRD): Sodium y Sable ya estaban en el pack y los mods funcionan sin Better Days.
- Se eliminó la entrada `cc-tweaked-compat`, un proyecto equivocado asociado por huella digital; CC: Tweaked se registra desde Modrinth.
- No se distribuyen `server.properties`, `defaultconfigs/`, `world/serverconfig/` ni los límites de render y simulación: son del servidor.

### Migración

Antes del primer sync hay que vaciar la carpeta `mods` del servidor y de cada instancia de Prism (conservando una copia de respaldo), porque el instalador no borra jars que él no instaló.