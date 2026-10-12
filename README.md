# MatiasWorld Pack

Modpack privado de **NeoForge 1.21.1** para un servidor de Minecraft entre amigos. Está gestionado con [packwiz](https://packwiz.infra.link/): el servidor y todas las instancias de Prism Launcher se sincronizan solos con este repositorio, así que nadie tiene que pasar mods ni configs a mano.

| | |
|---|---|
| Minecraft | 1.21.1 |
| Loader | NeoForge 21.1.248 |
| Mods | Gestionados por packwiz (casi todos desde Modrinth, unos pocos desde CurseForge) + 4 parches propios (jars locales) |
| Configs | 310 archivos en `config/` |

## Cómo funciona

    Administrador (PC)                 GitHub                        Destinos
    ------------------                 ------                        --------
    packwiz add / update   --push-->   pack.toml                -->  Servidor (al arrancar)
    editar config/                     index.toml (hashes)           packwiz-installer -s server
    packwiz refresh                    mods/*.pw.toml
                                       config/                  -->  Prism de cada jugador
                                                                     (al abrir el juego)
                                                                     packwiz-installer -s client

- `pack.toml` apunta a `index.toml`, que lista cada archivo con su hash.
- Cada mod es un pequeño `.pw.toml` con su URL de descarga (Modrinth o CurseForge), su hash y su **lado** (`client`, `server` o `both`). Los jars no se guardan en el repo, salvo 4 parches propios muy pequeños.
- El instalador compara el índice con lo que ya hay en disco, descarga lo nuevo o cambiado y **borra lo que ya no está en el pack**. Solo gestiona lo que él mismo instaló.
- Cada destino recibe solo lo que le corresponde: el servidor no descarga mods de cliente (Sodium, Iris...) y los clientes no descargan mods exclusivos del servidor (VoxelDash).

## Para jugadores: instalación

Se hace una sola vez. Después todo se actualiza solo al abrir el juego.

1. Instala [Prism Launcher](https://prismlauncher.org/).
2. Importa la instancia que te pase el administrador (clic derecho al exportar/importar). Si prefieres crearla a mano: Minecraft 1.21.1 + NeoForge 21.1.248.
3. Descarga `packwiz-installer-bootstrap.jar` desde <https://github.com/packwiz/packwiz-installer-bootstrap/releases> y cópialo a la carpeta `minecraft` de la instancia (clic derecho en la instancia > Carpeta > Minecraft).
4. En Editar instancia > Configuración > Comandos personalizados, marca la casilla y pon en **Comando de pre-lanzamiento**:

        "$INST_JAVA" -jar packwiz-installer-bootstrap.jar -g -s client https://raw.githubusercontent.com/matiascariboni/mc_modpack/master/pack.toml

El primer arranque tarda más porque descarga todo. Si el pre-lanzamiento falla (sin internet, por ejemplo), Prism no abre el juego: así nadie entra con mods desactualizados.

**Importante:** si vas a usar una instancia que ya tenía mods, vacía antes su carpeta `mods`. El instalador no borra jars que él no instaló y quedarían dos versiones del mismo mod.

## Qué se sincroniza y qué no

| Archivo | ¿Se distribuye? |
|---|---|
| Mods | Sí, según su `side` |
| Configs de gameplay (`config/`) | Sí: el servidor es la fuente de verdad y el pack sobrescribe en cada cliente |
| `defaultconfigs/` (claims de Open Parties and Claims, copiados de `world/serverconfig/` del servidor) | Sí: los mundos locales **nuevos** nacen con la misma protección que el servidor. Los mundos ya creados usan `saves/<mundo>/serverconfig/` y hay que copiarlos a mano |
| Configs de cliente que no afectan al rendimiento (`*-client.*` de Create, Mekanism, AE2, Sound Physics, partículas, etc.) | Sí: el pack las sobrescribe en cada cliente |
| Configs de rendimiento o personales (Sodium, Iris, Distant Horizons, Entity Culling, ETF/EMF, FerriteCore, JEI, spark, `neoforge-client.toml`, atajos de teclado, rutas locales) | No: cada jugador conserva la suya |
| `options.txt`, shaders, resource packs | No |
| `server.properties`, `world/`, configs de servidor del mundo | No: se editan solo en el servidor |

Los gráficos y la configuración de Distant Horizons de cada jugador **nunca se tocan**. Las configs de cliente que sí se distribuyen se sobrescriben en cada arranque, así que un jugador no puede cambiarlas de forma permanente. Los límites de render y simulación los marca el servidor en su `server.properties`.

La lista exacta de exclusiones está en `.packwizignore`.

### Mods por lado

- **Solo cliente:** Sodium, Iris, Iris Shader Folder, NeoContinuity, Entity Culling, Entity Texture Features, Attack Through Grass, BetterF3, AmbientSounds, Cubes Without Borders, Sound Physics Remastered, Storage Drawers Visual Tweaks.
- **Solo servidor:** VoxelDash.
- **Ambos:** todo lo demás, incluido Separate Sable Render Distance (SSRD).

## Para el administrador: flujo de trabajo

Trabaja siempre desde la carpeta local del modpack (fuera de Google Drive) y con el ejecutable de packwiz.

    packwiz modrinth add <mod>        # o: packwiz curseforge add <mod>
    packwiz update --all              # actualiza todos los mods
    packwiz refresh                   # obligatorio tras tocar cualquier archivo a mano
    git add .
    git commit -m "Descripción del cambio"
    git push

Luego reinicia el servidor (`sudo systemctl restart minecraft`) o espera a su próximo arranque: se sincroniza solo. Los jugadores lo reciben al abrir el juego.

Reglas útiles:

- **Edita configs solo en `config/` del repo**, nunca directamente en el servidor: el pack las sobrescribiría en el siguiente arranque.
- **Lados:** tras añadir un mod, revisa la línea `side` de su `.pw.toml`. Algunos mods de Modrinth declaran valores raros (`client_only_server_optional`) que packwiz marca como `both`; corrígelos a mano.
- **Fijar versión:** `packwiz pin <mod>` evita que `update --all` lo actualice.
- **Dependencias:** acepta las que packwiz ofrezca, salvo que ya estén en el pack desde otro origen. En ese caso responde que no, pero comprueba que la versión instalada cumple el mínimo que pide el mod (Subtle Effects 1.15 exigió Fzzy Config 0.7.7 o superior). Si el juego dice "requires X or above", actualiza X con `packwiz update <slug>`.
- **Cambios de Minecraft o NeoForge** no se sincronizan solos: hay que avisar a los jugadores para cambiar la versión en Prism.
- Anota cada cambio relevante en `CHANGELOG.md`.

## Instalar un mod nuevo (procedimiento con sus configs)

Un mod genera su config al arrancar. Si el repo trae un archivo escrito a mano o incompleto, el mod lo "corrige" en cada máquina (en el log aparece `is not correct. Correcting`) y el pack y las instancias se desincronizan. Por eso la config que se distribuye debe ser siempre **la que genera el propio mod**:

1. **Instala el mod en el pack:** `packwiz modrinth add <mod>`, revisa su `side` en `mods/<mod>.pw.toml`, `packwiz refresh`, commit y push.
2. **Lanza una instancia de cliente** (Prism) para que sincronice el pack y el mod genere sus archivos en `config/`. Revisa el log (`logs/latest.log`) por si el mod avisa de algo.
3. **Usa esos archivos como base:** copia los configs generados a una carpeta de trabajo y modifícalos a tu gusto. No escribas configs desde cero.
4. **Cópialos a `config/` del repo, `packwiz refresh`, commit y push.** Lanza otra vez la instancia y comprueba que el mod no vuelve a corregirlos: no debe haber `Correcting` en el log y el contenido de la instancia debe coincidir con el del repo.

Al copiar, aplica estas reglas:

- **Comparte** las configs de gameplay y las de cliente que no afectan al rendimiento.
- **Excluye** (en `.packwizignore`, antes del `refresh`) las de rendimiento o personales: gráficos, render, atajos de teclado, rutas locales, historiales y marcadores. Si dudas, abre el archivo y busca rutas (`C:\Users\...`) o nombres de jugador.
- **No copies** los `*.bak`, `*.bak.1` ni otros archivos de respaldo.
- Si el mod tiene una librería que también genera config, copia también la suya.

## Mods de CurseForge con descarga restringida

Algunos autores bloquean en CurseForge la descarga por terceros. Si hay alguno en el pack, el instalador falla con `This mod is excluded from the CurseForge API and must be downloaded manually` y el servidor no arranca. Por eso **se prefiere Modrinth**:

- Para añadir mods usa `packwiz modrinth add`.
- Para migrar uno existente: `packwiz remove <slug>` y luego `packwiz modrinth add <nombre>`. Modrinth localiza el mismo archivo por su hash SHA1 (`https://api.modrinth.com/v2/version_file/<sha1>?algorithm=sha1`).
- Para ver qué mods siguen en CurseForge:

        Select-String -Path mods\*.pw.toml -Pattern '^\[update\.curseforge\]'

- Si un mod solo existe en CurseForge y está bloqueado, busca una alternativa. No subas su jar al repo salvo que la licencia lo permita.
- Tras migrar, revisa la línea `side`: Modrinth declara entornos que packwiz no entiende (`client_only_server_optional`) y los deja en `both`.

## Actualizar mods: qué vigilar

- El servidor se sincroniza en cada arranque, así que un push con un error llega a él la próxima vez que se encienda. Prueba los cambios en tu cliente antes de publicar.
- Un mod puede exigir una versión mínima de otro (`requires X or above`): actualiza esa librería con `packwiz update <slug>`.
- Un mod puede migrar y resetear su propia config al actualizarse (pasó con Serene Seasons Plus 5.2.1: guardó la antigua como `.bak` y generó una nueva). Si ves ese aviso, copia la config nueva a `config/`, haz `refresh`, commit y push, para que el pack distribuya el formato nuevo y nadie más vea el reset.
## Qué excluir del pack (procedimiento)

Todo archivo o carpeta que exista en el repositorio y **no** esté en `.packwizignore` entra al índice con `packwiz refresh` y se distribuye al servidor y a todos los jugadores. Por eso, cada vez que añadas algo nuevo al repo (documentación, notas, scripts, configs...), sigue estos pasos:

1. **Decide si el servidor o los clientes lo necesitan** en su carpeta de juego. Si no, hay que excluirlo.
2. **Añade el patrón a `.packwizignore`** (sintaxis tipo `.gitignore`, rutas desde la raíz del repo). Ejemplo: `docs/`.
3. **Ejecuta `packwiz refresh`.**
4. **Verifica que no entró al índice.** Este comando no debe imprimir nada:

        Select-String -Path index.toml -Pattern '^file = "docs/'

   Y `git status` no debería mostrar `index.toml` como modificado si lo único que añadiste fue algo excluido.
5. **Haz commit y push** y anota el cambio en `CHANGELOG.md`.

Qué suele excluirse:

| Tipo | Ejemplos |
|---|---|
| Documentación y notas del repo | `docs/`, `README.md`, `CHANGELOG.md` |
| Configs de rendimiento o personales | Distant Horizons, Sodium, Iris, Entity Culling, ETF/EMF, JEI, `neoforge-client.toml`, `options.txt` |
| Datos del servidor | `server.properties`, `world/`, `whitelist.json`, `ops.json` |
| Bases de datos, logs y temporales | `*.db`, `*.log`, `*.bak`, `*.tmp`, `spark/` |
| Datos de infraestructura o personales | IDs de AWS, dominio, tokens, contraseñas, nombres de jugadores |

Aclaraciones:

- **`.packwizignore` decide qué se distribuye; `.gitignore` decide qué se sube a GitHub.** `docs/` va en el primero pero no en el segundo: se publica en el repo, pero no llega a los jugadores.
- Si excluyes un archivo que ya se había distribuido, el instalador lo borra en los destinos que lo recibieron, porque ya no está en el índice.
- El repositorio es público: nunca subas secretos ni datos de infraestructura, aunque el archivo esté excluido del pack. Excluirlo del pack no impide que suba a GitHub.
## Infraestructura del servidor (resumen)

El servidor corre en una instancia AWS EC2 con Amazon Linux y Java 21. Los detalles concretos (IDs, dominio, tokens) no se documentan aquí a propósito, porque el repositorio es público.

- **systemd** arranca y mantiene NeoForge (`Restart=on-failure`). Al pararlo, pide un `stop` por RCON y espera a que el mundo se guarde.
- **Memoria automática:** un script previo al arranque calcula el heap de Java a partir de la RAM de la instancia, dejando un margen reservado para el sistema. Si se cambia el tipo de instancia, no hay que tocar nada.
- **Auto-apagado por inactividad:** un temporizador revisa cada 15 minutos los jugadores conectados (por RCON). Si hay dos chequeos seguidos sin nadie, para el servidor y apaga la instancia (el disco y el mundo se conservan).
- **Arranque remoto:** una función Lambda con URL propia, protegida por un token, permite a los jugadores encender el servidor y consultar su estado (jugadores, RAM, CPU, disco, cuenta atrás de apagado) sin credenciales de AWS. Se reparte un pequeño cliente para Windows y otro para Mac/Linux.
- **VoxelDash** ofrece un panel web (métricas, consola, whitelist), publicado detrás de un proxy inverso con HTTPS automático.
- **Backups:** snapshots diarios del volumen EBS, con retención de 7.
- **Sincronización del pack:** el servicio de Minecraft ejecuta `packwiz-installer-bootstrap` con `-s server` como paso previo al arranque. Como el servidor se enciende y apaga solo, queda actualizado en cada arranque.

Fragmento del servicio (la URL es la misma que usan los clientes):

    ExecStartPre=/usr/bin/java -jar /home/ec2-user/minecraft_server/packwiz-installer-bootstrap.jar -g -s server https://raw.githubusercontent.com/matiascariboni/mc_modpack/master/pack.toml
    TimeoutStartSec=600

`TimeoutStartSec` debe ser alto: el primer sync descarga cientos de MB y el límite por defecto de systemd (90 s) lo mataría.

## Solución de problemas

- **Dos versiones del mismo mod / el juego no arranca:** hay jars viejos en `mods/` que el instalador no gestiona. Bórralos a mano y vuelve a abrir.
- **Error de hash en el instalador:** suele deberse a saltos de línea modificados por git. Este repo usa `.gitattributes` con `* -text` para evitarlo. Comprueba también que hiciste `packwiz refresh` antes del commit.
- **Los cambios no llegan:** la URL raw de GitHub tiene una caché de unos 5 minutos.
- **Un mod de CurseForge pide descarga manual:** su autor desactivó las descargas de terceros. Descárgalo a mano a la carpeta que indique el instalador, o busca el mismo mod en Modrinth.
- **El juego no abre:** casi siempre falla el pre-lanzamiento (sin internet o URL mal escrita). Lee el error que muestra Prism.

## Estructura del repositorio

    pack.toml          Metadatos del pack y versión de Minecraft/NeoForge
    index.toml         Índice de archivos con sus hashes (lo genera packwiz)
    mods/              Un .pw.toml por mod + 4 parches propios en .jar
    config/            Configs distribuidas a servidor y clientes
    .packwizignore     Qué NO entra al índice
    .gitignore         Qué NO sube a git
    .gitattributes     Evita conversión de saltos de línea
    CHANGELOG.md       Historial de cambios