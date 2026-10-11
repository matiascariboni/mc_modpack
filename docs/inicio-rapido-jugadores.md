# Inicio rápido: jugadores (Prism Launcher)

Instalación única, unos 10 minutos. Después, cada vez que abras el juego se actualizan solos los mods y las configs.

## Antes de empezar

- Cuenta de Minecraft Java y estar en la whitelist del servidor.
- [Prism Launcher](https://prismlauncher.org/) instalado.
- Conexión a internet al abrir el juego (el pre-lanzamiento descarga el pack).

## Opción A: importar la instancia del administrador (recomendada)

1. En Prism: **Añadir instancia > Importar** y selecciona el archivo que te pasó el administrador.
2. Abre la instancia y comprueba en **Editar instancia > Versión** que sea Minecraft 1.21.1 con NeoForge 21.1.248.
3. Salta al paso "Comprobar que funciona".

## Opción B: crear la instancia a mano

1. **Añadir instancia > Vanilla**, versión **1.21.1**.
2. **Editar instancia > Versión > Instalar Loader > NeoForge** y elige **21.1.248**.
3. Descarga `packwiz-installer-bootstrap.jar` desde <https://github.com/packwiz/packwiz-installer-bootstrap/releases> y cópialo en la carpeta de juego: clic derecho en la instancia > **Carpeta > Minecraft** (la que contiene `config` y `mods`).
4. **Editar instancia > Configuración > Comandos personalizados**, marca la casilla y en **Comando de pre-lanzamiento** pon:

        "$INST_JAVA" -jar packwiz-installer-bootstrap.jar -g -s client https://raw.githubusercontent.com/matiascariboni/mc_modpack/master/pack.toml

5. **Asigna memoria** en Configuración > Java (8 GB suele ir bien para este pack, si tu PC lo permite).

## Comprobar que funciona

1. Abre la instancia. El **primer arranque tarda varios minutos** porque descarga todos los mods.
2. Al terminar, la carpeta `mods` debe estar llena y el juego abre normal.
3. Conéctate al servidor.

## Qué NO se toca nunca

Tus gráficos (`options.txt`), shaders, resource packs, la configuración de Distant Horizons, Sodium, Iris y JEI son tuyos: el pack no los sobrescribe. Ajústalos a tu PC como quieras. El servidor marca sus propios límites de render y simulación.

## Si algo falla

| Síntoma | Qué hacer |
|---|---|
| Prism no abre el juego y muestra un error del pre-lanzamiento | Sin internet, URL mal escrita o el jar no está en la carpeta Minecraft. Revisa los pasos 3 y 4. |
| El juego no arranca por mods duplicados | Había mods viejos en `mods`. Borra el contenido de esa carpeta y abre de nuevo. |
| Error de versión al conectar | Comprueba la versión de NeoForge (21.1.248). Si el administrador la cambió, actualízala en Editar instancia > Versión. |
| Un mod pide descarga manual | Su autor restringe descargas de terceros. Avisa al administrador. |
| Quiero forzar una resincronización | Cierra el juego y ábrelo de nuevo: el pre-lanzamiento se ejecuta cada vez. |

Más detalles en el [README](../README.md).