# Inicio rápido: administrador

Resumen operativo del pack y del servidor. Detalles y motivos en el [README](../README.md).

## Requisitos en tu PC

- Git y el ejecutable `packwiz.exe` (builds en <https://nightly.link/packwiz/packwiz/workflows/go/main>).
- Clona el repo en una **ruta local**, no dentro de Google Drive:

        git clone https://github.com/matiascariboni/mc_modpack.git C:\dev\modpack

- Copia `packwiz.exe` dentro de esa carpeta (está en `.gitignore`, no se sube).

## Tareas habituales

Todas se hacen desde la carpeta del repo.

### Añadir un mod

    .\packwiz.exe modrinth add <nombre-o-url>
    .\packwiz.exe curseforge add <nombre-o-url>

1. Prefiere Modrinth (`modrinth add`): algunos autores bloquean en CurseForge la descarga por terceros y el servidor no arrancaría. Acepta las dependencias, salvo que ya estén en el pack con otro origen; en ese caso comprueba que la versión instalada cumple el mínimo que pide el mod.
2. Abre `mods\<mod>.pw.toml` y revisa la línea `side`: `client`, `server` o `both`. Corrígela si es un mod solo de cliente o solo de servidor.
3. Publica (ver "Publicar un cambio").

### Quitar un mod

    .\packwiz.exe remove <slug>

Publica. El instalador lo borra en el servidor y en los clientes.

### Actualizar mods

    .\packwiz.exe update --all
    .\packwiz.exe update <slug>
    .\packwiz.exe pin <slug>       # fija una versión para que update no la toque

Tras actualizar, prueba que el servidor y un cliente arrancan antes de avisar al grupo.

### Cambiar una configuración

1. Edita el archivo en `config\` del repo (nunca directamente en el servidor: el pack lo sobrescribiría).
2. Si es una config de cliente o personal, no debe estar en el pack: añádela a `.packwizignore`.
3. Publica.

### Añadir archivos nuevos al repo (docs, notas...)

Si no deben llegar a jugadores ni servidor, añádelos a `.packwizignore` antes del `refresh`. Procedimiento completo en la sección "Qué excluir del pack" del README.

### Publicar un cambio

    .\packwiz.exe refresh
    git add .
    git commit -m "Descripción del cambio"
    git push

Anota el cambio en `CHANGELOG.md` y sube la versión en `pack.toml` si procede. Espera unos 5 minutos por la caché de GitHub raw antes de probar.

Aplicar el cambio:

- **Servidor:** `sudo systemctl restart minecraft` (se sincroniza solo antes de arrancar). Avisa antes: expulsa a los jugadores conectados.
- **Jugadores:** les llega al abrir el juego.

### Cambiar la versión de Minecraft o NeoForge

No se sincroniza solo. Cambia `minecraft` o `neoforge` en `pack.toml` (o con `packwiz migrate`), actualiza el servidor y avisa a los jugadores para que cambien la versión en Prism.

## Servidor: configuración inicial (una sola vez; ya aplicada en el servidor actual)

El servidor corre en Linux bajo systemd. Los pasos son:

1. **Parar el servidor y apartar los mods antiguos.** El instalador no borra jars que él no instaló, y quedarían duplicados.

        sudo systemctl stop minecraft
        mv ~/minecraft_server/mods ~/mods_backup && mkdir ~/minecraft_server/mods

2. **Descargar el bootstrap:**

        cd ~/minecraft_server
        curl -L -o packwiz-installer-bootstrap.jar https://github.com/packwiz/packwiz-installer-bootstrap/releases/latest/download/packwiz-installer-bootstrap.jar

3. **Añadir el sync al servicio.** En `minecraft.service`, antes de `ExecStart`, y `TimeoutStartSec` en la sección `[Service]`:

        ExecStartPre=/usr/bin/java -jar /home/ec2-user/minecraft_server/packwiz-installer-bootstrap.jar -g -s server https://raw.githubusercontent.com/matiascariboni/mc_modpack/master/pack.toml
        TimeoutStartSec=600

   El timeout alto es necesario: el primer sync descarga cientos de MB y el límite por defecto (90 s) lo mataría.

4. **Recargar y arrancar:**

        sudo systemctl daemon-reload
        sudo systemctl start minecraft
        journalctl -u minecraft.service -f

5. Si arranca bien, puedes borrar `~/mods_backup` (no antes). No borres `packwiz.json` de la carpeta del servidor: es el registro de lo que instaló el instalador.

Si el sync falla, el arranque falla: el servidor nunca inicia con mods desactualizados.

## Mantenimiento del servidor

| Tarea | Comando |
|---|---|
| Estado | `sudo systemctl status minecraft` |
| Reiniciar (aplica cambios del pack) | `sudo systemctl restart minecraft` |
| Logs en vivo | `journalctl -u minecraft.service -f` |
| Editar `server.properties` | Parar el servidor, editar y arrancar. Si se edita con el servidor corriendo, Minecraft lo sobrescribe al apagarse. |
| Revisar el auto-apagado | `journalctl -u check-players.service -n 50 --no-pager` |

El heap de Java se calcula solo según la RAM de la instancia (script previo al arranque). El auto-apagado por inactividad y el arranque remoto están descritos en el README.

**Contraseñas y tokens:** la contraseña RCON debe coincidir en `minecraft.service`, `check-players.sh` y `server.properties`. Nunca las pegues en el repo, ni en `docs/`.

## Problemas frecuentes

| Síntoma | Causa y solución |
|---|---|
| Error de hash en el instalador | Se olvidó `packwiz refresh` antes del commit, o git alteró saltos de línea (el repo usa `* -text` para evitarlo). |
| Los cambios no llegan | Caché de GitHub raw (~5 min). Espera y reintenta. |
| Mod duplicado al añadir una dependencia | Responde "no" a dependencias que ya están en el pack con otro origen. Revisa duplicados por `filename` en `mods\*.pw.toml`. |
| Un mod de CurseForge pide descarga manual | El autor bloquea descargas de terceros. Busca el mismo mod en Modrinth. |
| Un mod de Modrinth queda en `both` con un valor de entorno raro | Corrige `side` a mano en su `.pw.toml`. |
| El servidor no arranca tras un cambio | Mira `journalctl -u minecraft.service -n 100`. Si falla el `ExecStartPre`, el problema es de red o del pack. |
| Volver atrás | `git revert <commit>`, `packwiz refresh` si hace falta, push y reinicia el servidor. |
| El servicio se reinicia en bucle | El sync falla (por ejemplo, un mod con descarga restringida) y `Restart=on-failure` reintenta cada 10 s. Ejecuta `sudo systemctl stop minecraft`, corrige el pack y repite el sync a mano para ver el error completo. |
| `This mod is excluded from the CurseForge API` | Migra ese mod a Modrinth (sección "Mods de CurseForge con descarga restringida" del README). |
| El juego dice `requires X or above` | Falta una versión mínima de una librería: `packwiz update <slug>`, refresh, commit y push. |
| Aviso de config reseteada tras actualizar un mod | El mod migró su config. Copia la nueva a `config/`, refresh, commit y push. |