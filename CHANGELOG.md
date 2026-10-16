# Changelog

Todos los cambios relevantes del modpack se anotan aquí. Formato basado en [Keep a Changelog](https://keepachangelog.com/es-ES/1.1.0/).

## [Sin publicar]

### Añadido

- WandyT Controller 1.0.0 (CurseForge, `both`). Permite espaciar las apariciones del comerciante errante (días entre spawns, distancia, permanencia y llamas). No existe en Modrinth, por eso es de CurseForge. Config en `config/wandytcontroller-common.toml`: aparece cada 15 días (`spawnIntervalDays = 15`), a 48 bloques del jugador (`spawnRadius = 48`) y se va a los 24000 ticks, 1 día (`despawnDelayTicks = 24000`). Spawn de vanilla bloqueado.
- `defaultconfigs/` con las 4 configs de Open Parties and Claims que usa el servidor en `world/serverconfig/` (versión 16, protección por defecto de claims activada). Antes el servidor tenía copias obsoletas (versión 15, sin protección) y los mundos locales nuevos nacían sin proteger. Solo afecta a mundos nuevos; los existentes usan `saves/<mundo>/serverconfig/`.
- Attack Through Grass 2.0.3 (Modrinth, solo cliente). Permite golpear a un mob aunque haya hierba, flores u otras plantas en su mismo bloque, en vez de romper la planta.
- Better Days 3.3.6.3 (Modrinth). Es el mod que aplica la velocidad del tiempo que calcula Serene Seasons Plus; sin él, `changeDayNightLengthsWithSeasons` no tenía efecto. Config en `config/betterdays-common.toml`: `clearWeatherOnWake = false` y `allowDaySleep = false`.

### Documentación

- Se explicita que siempre se prefieren las descargas de Modrinth y que CurseForge solo se usa si el mod no existe allí (README y guía del administrador).
- Nuevo procedimiento "Instalar un mod nuevo" en el README: instalar, push, lanzar una instancia, usar los configs generados por el mod como base y volver a publicar. La guía del administrador lo enlaza.
- Se quitan referencias personales o puntuales de la documentación (aviso sobre Google Drive y la ruta local `C:\dev\modpack` del clon).

### Cambiado

- `seasons.toml` queda con el orden de claves que escribe el mod y `fabric/indigo-renderer.properties` se excluye en `.packwizignore` (el mod reescribe su marca de fecha en cada arranque).

- Configs sincronizadas con las que generan los mods: se comparó cada config del pack con la de la instancia de Prism (308 de 311 ya eran idénticas) y `sereneseasons/seasons.toml`, `pipesnphysics-server.toml` y `fabric/indigo-renderer.properties` usan ahora el formato del archivo generado por el mod, conservando los valores del pack. `seasons.toml` incluye ya `grass_colour`, `grass_saturation`, `foliage_*` y `birch_color`; `pipesnphysics-server.toml`, las opciones nuevas `enableForeignPumps`, `foreignPumpStrengthScale` y `allowMixedPipeFluids`.

- Las configs de cliente que no afectan al rendimiento (61 archivos, de Create, Mekanism, AE2, Sound Physics, partículas, efectos visuales, etc.) ahora se distribuyen a los clientes. Se siguen excluyendo Sodium, Iris, Distant Horizons, Entity Culling, ETF/EMF, FerriteCore, JEI, spark, `neoforge-client.toml`, atajos de teclado y archivos con rutas locales. Se quitaron de `.packwizignore` `*-client.*`, `**/client.*` y Sound Physics.
- Serene Seasons: derretimiento de nieve y hielo mucho más agresivo (`config/sereneseasons/seasons.toml`). Early Spring pasa a 3 rolls al 35 % y `biome_temp_adjustment = 0.0` (antes -0.25); Mid Spring pasa a 3 rolls al 100 %; Late Spring y todo el verano, a 4 rolls al 100 %. El invierno y el otoño no cambian. A mitad de primavera casi no queda nieve ni hielo, salvo en biomas fríos y cumbres altas.
- Serene Seasons Plus: `keepFullDayNightCycleAtFixedLength = true` con `fullDayNightCycleLengthInRealMinutes = 20.0`. Todos los días duran 20 minutos reales y las estaciones solo cambian el reparto día/noche (de 33 % de luz en invierno a 67 % en verano, equivalente a unos 51° de latitud norte). Los 24 multiplicadores por subestación están calculados para las ventanas de Better Days (día 13000 ticks, noche 11000), por eso no se deben cambiar `dayStart` ni `nightStart`.

## [1.0.1] - 2026-10-11

### Añadido

- El servicio `minecraft.service` sincroniza el pack antes de arrancar (`ExecStartPre` con packwiz-installer y `TimeoutStartSec=600`). Si el sync falla, el servidor no arranca.
- README: secciones sobre mods de CurseForge con descarga restringida y sobre qué vigilar al actualizar mods.
- Guía de administrador y de jugadores ampliadas con nuevos problemas frecuentes.
- Carpeta `docs/` con documentación de referencia de mods (empezando por Open Parties and Claims). Está excluida en `.packwizignore`, así que no se distribuye a servidor ni clientes; solo vive en el repositorio.
- Sección en el README con el procedimiento para decidir y verificar qué archivos se excluyen del pack.

### Cambiado

- Casi todos los mods pasaron de CurseForge a Modrinth (mismo archivo, localizado por hash). Motivo: 11 mods tenían bloqueada la descarga por terceros y el servidor no arrancaba. Siguen en CurseForge: Gabou's Libs, NeoContinuity y Ore Generation Configuration.
- Versiones que cambiaron en la migración:

| Mod | Antes | Ahora |
|---|---|---|
| Create Aeronautics | 1.3.0 | 1.3.2 |
| Sable | 2.0.5 | 2.0.6 |
| JEI | 19.44.0.403 | 19.57.0.451 |
| Geckolib | 4.9.2 | 4.9.3 |
| Not Enough Animations | 1.12.4 | 1.12.6 |
| Subtle Effects | 1.14.3 | 1.15.0 |
| Fzzy Config | 0.7.6 | 0.7.7 |

### Notas

- Subtle Effects 1.15.0 exige Fzzy Config 0.7.7 o superior. Se aceptó mal la dependencia al migrar y el cliente no cargaba; se solucionó actualizando Fzzy Config.
- El instalador no borra jars que él no instaló: antes del primer sync hubo que vaciar `mods` en el servidor y en el cliente.
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