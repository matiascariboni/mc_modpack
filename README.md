# MatiasWorld Pack

Modpack privado de **NeoForge 1.21.1** para un servidor de Minecraft entre amigos. EstÃƒÂ¡ gestionado con [packwiz](https://packwiz.infra.link/): el servidor y todas las instancias de Prism Launcher se sincronizan solos con este repositorio, asÃƒÂ­ que nadie tiene que pasar mods ni configs a mano.

| | |
|---|---|
| Minecraft | 1.21.1 |
| Loader | NeoForge 21.1.248 |
| Mods | 177 gestionados por packwiz + 4 parches propios (jars locales) |
| Configs | 246 archivos en `config/` |

## CÃƒÂ³mo funciona

    Administrador (PC)                 GitHub                        Destinos
    ------------------                 ------                        --------
    packwiz add / update   --push-->   pack.toml                -->  Servidor (al arrancar)
    editar config/                     index.toml (hashes)           packwiz-installer -s server
    packwiz refresh                    mods/*.pw.toml
                                       config/                  -->  Prism de cada jugador
                                                                     (al abrir el juego)
                                                                     packwiz-installer -s client

- `pack.toml` apunta a `index.toml`, que lista cada archivo con su hash.
- Cada mod es un pequeÃƒÂ±o `.pw.toml` con su URL de descarga (Modrinth o CurseForge), su hash y su **lado** (`client`, `server` o `both`). Los jars no se guardan en el repo, salvo 4 parches propios muy pequeÃƒÂ±os.
- El instalador compara el ÃƒÂ­ndice con lo que ya hay en disco, descarga lo nuevo o cambiado y **borra lo que ya no estÃƒÂ¡ en el pack**. Solo gestiona lo que ÃƒÂ©l mismo instalÃƒÂ³.
- Cada destino recibe solo lo que le corresponde: el servidor no descarga mods de cliente (Sodium, Iris...) y los clientes no descargan mods exclusivos del servidor (VoxelDash).

## Para jugadores: instalaciÃƒÂ³n

Se hace una sola vez. DespuÃƒÂ©s todo se actualiza solo al abrir el juego.

1. Instala [Prism Launcher](https://prismlauncher.org/).
2. Importa la instancia que te pase el administrador (clic derecho al exportar/importar). Si prefieres crearla a mano: Minecraft 1.21.1 + NeoForge 21.1.248.
3. Descarga `packwiz-installer-bootstrap.jar` desde <https://github.com/packwiz/packwiz-installer-bootstrap/releases> y cÃƒÂ³pialo a la carpeta `minecraft` de la instancia (clic derecho en la instancia > Carpeta > Minecraft).
4. En Editar instancia > ConfiguraciÃƒÂ³n > Comandos personalizados, marca la casilla y pon en **Comando de pre-lanzamiento**:

        "$INST_JAVA" -jar packwiz-installer-bootstrap.jar -g -s client https://raw.githubusercontent.com/matiascariboni/mc_modpack/master/pack.toml

El primer arranque tarda mÃƒÂ¡s porque descarga todo. Si el pre-lanzamiento falla (sin internet, por ejemplo), Prism no abre el juego: asÃƒÂ­ nadie entra con mods desactualizados.

**Importante:** si vas a usar una instancia que ya tenÃƒÂ­a mods, vacÃƒÂ­a antes su carpeta `mods`. El instalador no borra jars que ÃƒÂ©l no instalÃƒÂ³ y quedarÃƒÂ­an dos versiones del mismo mod.

## QuÃƒÂ© se sincroniza y quÃƒÂ© no

| Archivo | Ã‚Â¿Se distribuye? |
|---|---|
| Mods | SÃƒÂ­, segÃƒÂºn su `side` |
| Configs de gameplay (`config/`) | SÃƒÂ­: el servidor es la fuente de verdad y el pack sobrescribe en cada cliente |
| Configs de cliente (`*-client.*`, Sodium, Iris, JEI, Distant Horizons...) | No: cada jugador conserva la suya |
| `options.txt`, shaders, resource packs | No |
| `server.properties`, `world/`, configs de servidor del mundo | No: se editan solo en el servidor |

Los grÃƒÂ¡ficos y la configuraciÃƒÂ³n de Distant Horizons de cada jugador **nunca se tocan**. Los lÃƒÂ­mites de render y simulaciÃƒÂ³n los marca el servidor en su `server.properties`.

La lista exacta de exclusiones estÃƒÂ¡ en `.packwizignore`.

### Mods por lado

- **Solo cliente:** Sodium, Iris, Iris Shader Folder, NeoContinuity, Entity Culling, Entity Texture Features, BetterF3, AmbientSounds, Cubes Without Borders, Sound Physics Remastered, Storage Drawers Visual Tweaks.
- **Solo servidor:** VoxelDash.
- **Ambos:** todo lo demÃƒÂ¡s, incluido Separate Sable Render Distance (SSRD).

## Para el administrador: flujo de trabajo

Trabaja siempre desde la carpeta local del modpack (fuera de Google Drive) y con el ejecutable de packwiz.

    packwiz modrinth add <mod>        # o: packwiz curseforge add <mod>
    packwiz update --all              # actualiza todos los mods
    packwiz refresh                   # obligatorio tras tocar cualquier archivo a mano
    git add .
    git commit -m "DescripciÃƒÂ³n del cambio"
    git push

Luego reinicia el servidor (`sudo systemctl restart minecraft`) o espera a su prÃƒÂ³ximo arranque: se sincroniza solo. Los jugadores lo reciben al abrir el juego.

Reglas ÃƒÂºtiles:

- **Edita configs solo en `config/` del repo**, nunca directamente en el servidor: el pack las sobrescribirÃƒÂ­a en el siguiente arranque.
- **Lados:** tras aÃƒÂ±adir un mod, revisa la lÃƒÂ­nea `side` de su `.pw.toml`. Algunos mods de Modrinth declaran valores raros (`client_only_server_optional`) que packwiz marca como `both`; corrÃƒÂ­gelos a mano.
- **Fijar versiÃƒÂ³n:** `packwiz pin <mod>` evita que `update --all` lo actualice.
- **Dependencias duplicadas:** si packwiz ofrece aÃƒÂ±adir una dependencia que ya estÃƒÂ¡ en el pack con otro origen, responde que no.
- **Cambios de Minecraft o NeoForge** no se sincronizan solos: hay que avisar a los jugadores para cambiar la versiÃƒÂ³n en Prism.
- Anota cada cambio relevante en `CHANGELOG.md`.

## Infraestructura del servidor (resumen)

El servidor corre en una instancia AWS EC2 con Amazon Linux y Java 21. Los detalles concretos (IDs, dominio, tokens) no se documentan aquÃƒÂ­ a propÃƒÂ³sito, porque el repositorio es pÃƒÂºblico.

- **systemd** arranca y mantiene NeoForge (`Restart=on-failure`). Al pararlo, pide un `stop` por RCON y espera a que el mundo se guarde.
- **Memoria automÃƒÂ¡tica:** un script previo al arranque calcula el heap de Java a partir de la RAM de la instancia, dejando un margen reservado para el sistema. Si se cambia el tipo de instancia, no hay que tocar nada.
- **Auto-apagado por inactividad:** un temporizador revisa cada 15 minutos los jugadores conectados (por RCON). Si hay dos chequeos seguidos sin nadie, para el servidor y apaga la instancia (el disco y el mundo se conservan).
- **Arranque remoto:** una funciÃƒÂ³n Lambda con URL propia, protegida por un token, permite a los jugadores encender el servidor y consultar su estado (jugadores, RAM, CPU, disco, cuenta atrÃƒÂ¡s de apagado) sin credenciales de AWS. Se reparte un pequeÃƒÂ±o cliente para Windows y otro para Mac/Linux.
- **VoxelDash** ofrece un panel web (mÃƒÂ©tricas, consola, whitelist), publicado detrÃƒÂ¡s de un proxy inverso con HTTPS automÃƒÂ¡tico.
- **Backups:** snapshots diarios del volumen EBS, con retenciÃƒÂ³n de 7.
- **SincronizaciÃƒÂ³n del pack:** el servicio de Minecraft ejecuta `packwiz-installer-bootstrap` con `-s server` como paso previo al arranque. Como el servidor se enciende y apaga solo, queda actualizado en cada arranque.

Fragmento del servicio (la URL es la misma que usan los clientes):

    ExecStartPre=/usr/bin/java -jar /home/ec2-user/minecraft_server/packwiz-installer-bootstrap.jar -g -s server https://raw.githubusercontent.com/matiascariboni/mc_modpack/master/pack.toml
    TimeoutStartSec=600

`TimeoutStartSec` debe ser alto: el primer sync descarga cientos de MB y el lÃƒÂ­mite por defecto de systemd (90 s) lo matarÃƒÂ­a.

## SoluciÃƒÂ³n de problemas

- **Dos versiones del mismo mod / el juego no arranca:** hay jars viejos en `mods/` que el instalador no gestiona. BÃƒÂ³rralos a mano y vuelve a abrir.
- **Error de hash en el instalador:** suele deberse a saltos de lÃƒÂ­nea modificados por git. Este repo usa `.gitattributes` con `* -text` para evitarlo. Comprueba tambiÃƒÂ©n que hiciste `packwiz refresh` antes del commit.
- **Los cambios no llegan:** la URL raw de GitHub tiene una cachÃƒÂ© de unos 5 minutos.
- **Un mod de CurseForge pide descarga manual:** su autor desactivÃƒÂ³ las descargas de terceros. DescÃƒÂ¡rgalo a mano a la carpeta que indique el instalador, o busca el mismo mod en Modrinth.
- **El juego no abre:** casi siempre falla el pre-lanzamiento (sin internet o URL mal escrita). Lee el error que muestra Prism.

## Estructura del repositorio

    pack.toml          Metadatos del pack y versiÃƒÂ³n de Minecraft/NeoForge
    index.toml         ÃƒÂndice de archivos con sus hashes (lo genera packwiz)
    mods/              Un .pw.toml por mod + 4 parches propios en .jar
    config/            Configs distribuidas a servidor y clientes
    .packwizignore     QuÃƒÂ© NO entra al ÃƒÂ­ndice
    .gitignore         QuÃƒÂ© NO sube a git
    .gitattributes     Evita conversiÃƒÂ³n de saltos de lÃƒÂ­nea
    CHANGELOG.md       Historial de cambios