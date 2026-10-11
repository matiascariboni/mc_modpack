# MatiasWorld Pack

Modpack privado de **NeoForge 1.21.1** para un servidor de Minecraft entre amigos. EstÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â¡ gestionado con [packwiz](https://packwiz.infra.link/): el servidor y todas las instancias de Prism Launcher se sincronizan solos con este repositorio, asÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â­ que nadie tiene que pasar mods ni configs a mano.

| | |
|---|---|
| Minecraft | 1.21.1 |
| Loader | NeoForge 21.1.248 |
| Mods | 177 gestionados por packwiz + 4 parches propios (jars locales) |
| Configs | 246 archivos en `config/` |

## CÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³mo funciona

    Administrador (PC)                 GitHub                        Destinos
    ------------------                 ------                        --------
    packwiz add / update   --push-->   pack.toml                -->  Servidor (al arrancar)
    editar config/                     index.toml (hashes)           packwiz-installer -s server
    packwiz refresh                    mods/*.pw.toml
                                       config/                  -->  Prism de cada jugador
                                                                     (al abrir el juego)
                                                                     packwiz-installer -s client

- `pack.toml` apunta a `index.toml`, que lista cada archivo con su hash.
- Cada mod es un pequeÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â±o `.pw.toml` con su URL de descarga (Modrinth o CurseForge), su hash y su **lado** (`client`, `server` o `both`). Los jars no se guardan en el repo, salvo 4 parches propios muy pequeÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â±os.
- El instalador compara el ÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â­ndice con lo que ya hay en disco, descarga lo nuevo o cambiado y **borra lo que ya no estÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â¡ en el pack**. Solo gestiona lo que ÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â©l mismo instalÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³.
- Cada destino recibe solo lo que le corresponde: el servidor no descarga mods de cliente (Sodium, Iris...) y los clientes no descargan mods exclusivos del servidor (VoxelDash).

## Para jugadores: instalaciÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³n

Se hace una sola vez. DespuÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â©s todo se actualiza solo al abrir el juego.

1. Instala [Prism Launcher](https://prismlauncher.org/).
2. Importa la instancia que te pase el administrador (clic derecho al exportar/importar). Si prefieres crearla a mano: Minecraft 1.21.1 + NeoForge 21.1.248.
3. Descarga `packwiz-installer-bootstrap.jar` desde <https://github.com/packwiz/packwiz-installer-bootstrap/releases> y cÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³pialo a la carpeta `minecraft` de la instancia (clic derecho en la instancia > Carpeta > Minecraft).
4. En Editar instancia > ConfiguraciÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³n > Comandos personalizados, marca la casilla y pon en **Comando de pre-lanzamiento**:

        "$INST_JAVA" -jar packwiz-installer-bootstrap.jar -g -s client https://raw.githubusercontent.com/matiascariboni/mc_modpack/master/pack.toml

El primer arranque tarda mÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â¡s porque descarga todo. Si el pre-lanzamiento falla (sin internet, por ejemplo), Prism no abre el juego: asÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â­ nadie entra con mods desactualizados.

**Importante:** si vas a usar una instancia que ya tenÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â­a mods, vacÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â­a antes su carpeta `mods`. El instalador no borra jars que ÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â©l no instalÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³ y quedarÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â­an dos versiones del mismo mod.

## QuÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â© se sincroniza y quÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â© no

| Archivo | ÃƒÆ’Ã†â€™ÃƒÂ¢Ã¢â€šÂ¬Ã…Â¡ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â¿Se distribuye? |
|---|---|
| Mods | SÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â­, segÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Âºn su `side` |
| Configs de gameplay (`config/`) | SÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â­: el servidor es la fuente de verdad y el pack sobrescribe en cada cliente |
| Configs de cliente (`*-client.*`, Sodium, Iris, JEI, Distant Horizons...) | No: cada jugador conserva la suya |
| `options.txt`, shaders, resource packs | No |
| `server.properties`, `world/`, configs de servidor del mundo | No: se editan solo en el servidor |

Los grÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â¡ficos y la configuraciÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³n de Distant Horizons de cada jugador **nunca se tocan**. Los lÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â­mites de render y simulaciÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³n los marca el servidor en su `server.properties`.

La lista exacta de exclusiones estÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â¡ en `.packwizignore`.

### Mods por lado

- **Solo cliente:** Sodium, Iris, Iris Shader Folder, NeoContinuity, Entity Culling, Entity Texture Features, BetterF3, AmbientSounds, Cubes Without Borders, Sound Physics Remastered, Storage Drawers Visual Tweaks.
- **Solo servidor:** VoxelDash.
- **Ambos:** todo lo demÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â¡s, incluido Separate Sable Render Distance (SSRD).

## Para el administrador: flujo de trabajo

Trabaja siempre desde la carpeta local del modpack (fuera de Google Drive) y con el ejecutable de packwiz.

    packwiz modrinth add <mod>        # o: packwiz curseforge add <mod>
    packwiz update --all              # actualiza todos los mods
    packwiz refresh                   # obligatorio tras tocar cualquier archivo a mano
    git add .
    git commit -m "DescripciÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³n del cambio"
    git push

Luego reinicia el servidor (`sudo systemctl restart minecraft`) o espera a su prÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³ximo arranque: se sincroniza solo. Los jugadores lo reciben al abrir el juego.

Reglas ÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Âºtiles:

- **Edita configs solo en `config/` del repo**, nunca directamente en el servidor: el pack las sobrescribirÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â­a en el siguiente arranque.
- **Lados:** tras aÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â±adir un mod, revisa la lÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â­nea `side` de su `.pw.toml`. Algunos mods de Modrinth declaran valores raros (`client_only_server_optional`) que packwiz marca como `both`; corrÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â­gelos a mano.
- **Fijar versiÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³n:** `packwiz pin <mod>` evita que `update --all` lo actualice.
- **Dependencias duplicadas:** si packwiz ofrece aÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â±adir una dependencia que ya estÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â¡ en el pack con otro origen, responde que no.
- **Cambios de Minecraft o NeoForge** no se sincronizan solos: hay que avisar a los jugadores para cambiar la versiÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³n en Prism.
- Anota cada cambio relevante en `CHANGELOG.md`.

## QuÃƒÂ© excluir del pack (procedimiento)

Todo archivo o carpeta que exista en el repositorio y **no** estÃƒÂ© en `.packwizignore` entra al ÃƒÂ­ndice con `packwiz refresh` y se distribuye al servidor y a todos los jugadores. Por eso, cada vez que aÃƒÂ±adas algo nuevo al repo (documentaciÃƒÂ³n, notas, scripts, configs...), sigue estos pasos:

1. **Decide si el servidor o los clientes lo necesitan** en su carpeta de juego. Si no, hay que excluirlo.
2. **AÃƒÂ±ade el patrÃƒÂ³n a `.packwizignore`** (sintaxis tipo `.gitignore`, rutas desde la raÃƒÂ­z del repo). Ejemplo: `docs/`.
3. **Ejecuta `packwiz refresh`.**
4. **Verifica que no entrÃƒÂ³ al ÃƒÂ­ndice.** Este comando no debe imprimir nada:

        Select-String -Path index.toml -Pattern '^file = "docs/'

   Y `git status` no deberÃƒÂ­a mostrar `index.toml` como modificado si lo ÃƒÂºnico que aÃƒÂ±adiste fue algo excluido.
5. **Haz commit y push** y anota el cambio en `CHANGELOG.md`.

QuÃƒÂ© suele excluirse:

| Tipo | Ejemplos |
|---|---|
| DocumentaciÃƒÂ³n y notas del repo | `docs/`, `README.md`, `CHANGELOG.md` |
| Configs de cliente o personales | `*-client.*`, Distant Horizons, Sodium, Iris, JEI, `options.txt` |
| Datos del servidor | `server.properties`, `world/`, `defaultconfigs/`, `whitelist.json`, `ops.json` |
| Bases de datos, logs y temporales | `*.db`, `*.log`, `*.bak`, `*.tmp`, `spark/` |
| Datos de infraestructura o personales | IDs de AWS, dominio, tokens, contraseÃƒÂ±as, nombres de jugadores |

Aclaraciones:

- **`.packwizignore` decide quÃƒÂ© se distribuye; `.gitignore` decide quÃƒÂ© se sube a GitHub.** `docs/` va en el primero pero no en el segundo: se publica en el repo, pero no llega a los jugadores.
- Si excluyes un archivo que ya se habÃƒÂ­a distribuido, el instalador lo borra en los destinos que lo recibieron, porque ya no estÃƒÂ¡ en el ÃƒÂ­ndice.
- El repositorio es pÃƒÂºblico: nunca subas secretos ni datos de infraestructura, aunque el archivo estÃƒÂ© excluido del pack. Excluirlo del pack no impide que suba a GitHub.
## Infraestructura del servidor (resumen)

El servidor corre en una instancia AWS EC2 con Amazon Linux y Java 21. Los detalles concretos (IDs, dominio, tokens) no se documentan aquÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â­ a propÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³sito, porque el repositorio es pÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Âºblico.

- **systemd** arranca y mantiene NeoForge (`Restart=on-failure`). Al pararlo, pide un `stop` por RCON y espera a que el mundo se guarde.
- **Memoria automÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â¡tica:** un script previo al arranque calcula el heap de Java a partir de la RAM de la instancia, dejando un margen reservado para el sistema. Si se cambia el tipo de instancia, no hay que tocar nada.
- **Auto-apagado por inactividad:** un temporizador revisa cada 15 minutos los jugadores conectados (por RCON). Si hay dos chequeos seguidos sin nadie, para el servidor y apaga la instancia (el disco y el mundo se conservan).
- **Arranque remoto:** una funciÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³n Lambda con URL propia, protegida por un token, permite a los jugadores encender el servidor y consultar su estado (jugadores, RAM, CPU, disco, cuenta atrÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â¡s de apagado) sin credenciales de AWS. Se reparte un pequeÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â±o cliente para Windows y otro para Mac/Linux.
- **VoxelDash** ofrece un panel web (mÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â©tricas, consola, whitelist), publicado detrÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â¡s de un proxy inverso con HTTPS automÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â¡tico.
- **Backups:** snapshots diarios del volumen EBS, con retenciÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³n de 7.
- **SincronizaciÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³n del pack:** el servicio de Minecraft ejecuta `packwiz-installer-bootstrap` con `-s server` como paso previo al arranque. Como el servidor se enciende y apaga solo, queda actualizado en cada arranque.

Fragmento del servicio (la URL es la misma que usan los clientes):

    ExecStartPre=/usr/bin/java -jar /home/ec2-user/minecraft_server/packwiz-installer-bootstrap.jar -g -s server https://raw.githubusercontent.com/matiascariboni/mc_modpack/master/pack.toml
    TimeoutStartSec=600

`TimeoutStartSec` debe ser alto: el primer sync descarga cientos de MB y el lÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â­mite por defecto de systemd (90 s) lo matarÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â­a.

## SoluciÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³n de problemas

- **Dos versiones del mismo mod / el juego no arranca:** hay jars viejos en `mods/` que el instalador no gestiona. BÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³rralos a mano y vuelve a abrir.
- **Error de hash en el instalador:** suele deberse a saltos de lÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â­nea modificados por git. Este repo usa `.gitattributes` con `* -text` para evitarlo. Comprueba tambiÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â©n que hiciste `packwiz refresh` antes del commit.
- **Los cambios no llegan:** la URL raw de GitHub tiene una cachÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â© de unos 5 minutos.
- **Un mod de CurseForge pide descarga manual:** su autor desactivÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³ las descargas de terceros. DescÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â¡rgalo a mano a la carpeta que indique el instalador, o busca el mismo mod en Modrinth.
- **El juego no abre:** casi siempre falla el pre-lanzamiento (sin internet o URL mal escrita). Lee el error que muestra Prism.

## Estructura del repositorio

    pack.toml          Metadatos del pack y versiÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³n de Minecraft/NeoForge
    index.toml         ÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Ândice de archivos con sus hashes (lo genera packwiz)
    mods/              Un .pw.toml por mod + 4 parches propios en .jar
    config/            Configs distribuidas a servidor y clientes
    .packwizignore     QuÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â© NO entra al ÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â­ndice
    .gitignore         QuÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â© NO sube a git
    .gitattributes     Evita conversiÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â³n de saltos de lÃƒÆ’Ã†â€™Ãƒâ€ Ã¢â‚¬â„¢ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â­nea
    CHANGELOG.md       Historial de cambios