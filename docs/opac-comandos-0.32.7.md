# Open Parties and Claims (OPAC) — Referencia de comandos

Generado analizando el JAR `open-parties-and-claims-neoforge-1_21_1-0_32_7.jar` (decompilado con Vineflower + archivo de idioma `en_us.json`).

## 0. Leer primero

- **Versión:** este documento es de la **0.32.7**, que es la que corre ahora en el servidor (actualizado desde la 0.30.3; ver sección 12). Los comandos de anclas (`/oclaims anchor`, `/oclaims unanchor`) no existían en la 0.30.3. Lo que ves con Tab en el juego manda sobre este documento.
- **Marcas usadas:** ✅ confirmado en el código decompilado · ⚠️ inferido/no probado en juego (verifícalo con Tab).
- **Nivel op = permiso 2** (`hasPermission(2)`). Todo lo marcado «op» exige ser operador (o permiso equivalente).
- Sintaxis: `<obligatorio>`, `[opcional]`, `a|b` alternativas.
- Los comandos de claims están desactivados si `enabled = false` dentro de `[serverConfig.claims]` en `openpartiesandclaims-server.toml` (en el código la opción se llama `claimsEnabled`).

---

## 1. Claims: `/oclaims claim` y `/oclaims unclaim`

### 1.1 Sintaxis completa ✅

```
/oclaims [<modo>] claim   [as <jugador>] [with <sub-id>] [in <dimensión>] [<from-block-pos> <to-block-pos>] [anyway]
/oclaims [<modo>] unclaim [as <jugador>]                  [in <dimensión>] [<from-block-pos> <to-block-pos>] [anyway]
```

| Parte | Significado | Quién puede |
|---|---|---|
| `<modo>` | `player`, `party` o `server`. Si se omite, usa tu modo actual (ver 1.4) | `player`: todos · `party`: solo si `partyOwnedClaims = true` · `server`: op o permiso `server claim` |
| `as <jugador>` | Reclama en nombre de otro jugador | op o permiso de impersonación |
| `with <sub-id>` | Usa una sub-configuración de claims (solo `claim`). `main` = la principal | todos |
| `in <dimensión>` | Dimensión del área (p. ej. `minecraft:overworld`) | todos |
| `<from-block-pos> <to-block-pos>` | Esquinas opuestas del área, en **coordenadas de BLOQUE** (X Z) | todos |
| `anyway` | Salta la comprobación de distancia/dimensión | **solo op** (nivel 2) |

Formas válidas más comunes:

```
/oclaims claim                                   # solo el chunk donde estás
/oclaims claim 385 -775 495 -895                 # área rectangular (ejemplo real que funcionó)
/oclaims claim with mi-sub 385 -775 495 -895     # área con sub-claim
/oclaims unclaim 385 -775 495 -895               # liberar un área
```

### 1.2 Reglas importantes

- ✅ Las coordenadas son de **bloque**; el mod las convierte a chunk con `>> 4` (dividir entre 16 redondeando hacia abajo) y toma el rectángulo mínimo que contiene ambas esquinas. Da igual qué esquina pongas primero.
- ✅ Tamaño del área en chunks = `(|X2chunk − X1chunk| + 1) × (|Z2chunk − Z1chunk| + 1)`. Debe ser ≤ `maxSingleClaimActionSize`.
- ✅ Sin coordenadas (`/oclaims claim`) → solo tu chunk actual.
- ✅ En 0.32.7 el código no registra una forma con una sola coordenada; `from` y `to` van siempre juntos (la 0.30.3 mostraba `from` entre corchetes en el Tab). Usa siempre las dos.
- ✅ El área se procesa como **tarea en segundo plano**: ves «Began claiming…» y luego «Finished… Here are the results». Aparece un botón rojo **[INTERRUPT]** para cancelarla.
- ✅ Solo puedes tener **una tarea de área en curso** a la vez («You have an over-area claim action task currently in progress»).
- ⚠️ La distancia máxima (`maxClaimDistance`) se mide desde tu posición actual; el código usa tu chunk como origen. En tu prueba funcionó estando en el centro de una zona de 7×8 chunks con `maxClaimDistance = 5`.
- ✅ Debes estar en la misma dimensión que el área (si no, `anyway`, que es solo de op).
- ✅ No puedes liberar (`unclaim`) un chunk que no es tuyo ni uno sin reclamar. Si estás por encima del límite de claims, solo puedes liberar.

### 1.3 Calcular tu zona (bloque → chunk)

```
chunkX = floor(bloqueX / 16)        chunkZ = floor(bloqueZ / 16)
```

Tu ejemplo: bloques (385, -775) → chunk (24, -49)… y (495, -895) → chunk (30, -56). Área = 7 × 8 = 56 chunks (coincide con el mensaje del mod: «from (24, −56) to (30, −49)»).

Atajo en el juego: F3 muestra «Chunk» (coordenadas de chunk) y «XYZ» (bloques).

### 1.4 Modos de claim (¿a nombre de quién reclamas?)

```
/oclaims default-claim-mode      # modo automático (jugador o party según corresponda)
/oclaims player-claim-mode       # reclamar como jugador
/oclaims party-claim-mode        # reclamar como party   (solo si partyOwnedClaims = true)
/oclaims server-claim-mode       # reclamar como servidor (op o permiso)
```

✅ Cada uno alterna (activa/desactiva). En tu servidor `partyOwnedClaims = false`, así que `party` no está disponible.

---

## 2. Forceload (mantener chunks cargados) ✅

```
/oclaims [<modo>] forceload   [as <jugador>] [in <dimensión>] [<from-block-pos> <to-block-pos>] [anyway]
/oclaims [<modo>] unforceload [as <jugador>] [in <dimensión>] [<from-block-pos> <to-block-pos>] [anyway]
```

- Mismas reglas de área, tarea en segundo plano e `[INTERRUPT]` que `claim`.
- ✅ Solo funciona en chunks **que ya reclamaste** («You need to claim the target chunk(s) first!»).
- Límite por jugador: `maxPlayerClaimForceloads` (en tu config = 5).
- ⚠️ Con tu servidor que se apaga sin jugadores, forceload no sirve mientras la instancia esté parada; además la opción «Forceload While Offline» del config de jugador (`claims.forceload.offlineForceload`) puede estar limitada por el servidor.

---

## 3. Anclas de claim (desde 0.32.x) ✅

```
/oclaims [<modo>] anchor   [as <jugador>] [in <dimensión>] [<block-pos>]
/oclaims [<modo>] unanchor [as <jugador>] [in <dimensión>] [<block-pos>] confirm
```

- Solo aplica si el servidor tiene `anchorBasedClaiming = true`. Si no: «Claiming anchors are not enabled on the server!». **En este servidor está en `false`** (desactivadas).
- ✅ Según la descripción oficial del mod (Modrinth), con `anchorBasedClaiming = true` cada jugador tiene un número limitado de anclas por dimensión y solo puede reclamar alrededor de ellas; sirve para limitar cuántas áreas separadas puede tener. El límite de una dimensión concreta se ajusta creando una sub-config en la config de zona sin reclamar (wilderness) con el ID de la dimensión y la opción «+ Claiming Anchors». El rango se ajusta con `claimingAnchorRange` y los dos límites se pueden sobrescribir con los permisos `maxPlayerClaimingAnchorsPermission` y `claimingAnchorRangePermission`. Las anclas llegaron con la 0.32.7 (los mapas de Xaero añadieron su soporte el 5-oct-2026). ✅ Según los comentarios del propio `server.toml`: la función sirve para evitar que los jugadores hagan muchos claims pequeños repartidos por el mundo, los jugadores eligen dónde colocar sus anclas, y el área alcanzable desde cada ancla es un **cuadrado** (rango en cada eje). Los valores por defecto son 3 anclas por dimensión y rango 16.
- Con anclas, solo puedes reclamar dentro del rango de un ancla («Use /oclaims anchor to place a claiming anchor»).
- `unanchor` exige `confirm` y **borra los claims que solo estén en el rango de esa ancla**. No se puede deshacer.
- Los claims de servidor no usan anclas.

---

## 4. Otros comandos de `/oclaims`

| Comando | Qué hace | Quién |
|---|---|---|
| `/oclaims about` ✅ | Resumen de tus claims: nombre, color, número de claims/forceloads y límites | todos |
| `/oclaims about <perfil>` ✅ | Lo mismo de otro jugador | op |
| `/oclaims interrupt` ⚠️ | Interrumpe tu tarea de área en curso (el botón [INTERRUPT] lo sugiere ya escrito). Puede existir una variante para otro jugador (`for <jugador>`), solo op | todos (propia) |
| `/oclaims sub-claim current` ⚠️ | Muestra qué sub-config usas para nuevos claims | todos |
| `/oclaims sub-claim use <sub-id>` ⚠️ | Cambia la sub-config usada en nuevos claims (si no existe: «The input sub-config does not exist!») | todos |
| `/oclaims clear` ✅ | **Borra TODOS tus claims.** Sin `confirm` solo avisa | todos |
| `/oclaims clear confirm` ✅ | Confirma el borrado total. **Irreversible** | todos |
| `/oclaims clear for <perfil> [confirm]` ✅ | Borra todos los claims de otro jugador | op o modo admin |
| `/oclaims transfer <jugador> [confirm]` ✅ | Transfiere todos tus claims a otro jugador conectado; él debe aceptar (botón o `/oclaims transfer-accept <id>`). Copia también tus grupos y sub-configs | todos |
| `/oclaims transfer-accept <player-id>` ✅ | Acepta una transferencia recibida (normalmente se hace pulsando el botón del chat) | receptor |
| `/oclaims teleport <perfil>` ✅ | Te teletransporta a un chunk reclamado por ese jugador. Solo en creativo o invulnerable | op o permiso `teleport` |
| `/oclaims admin-mode` ✅ | Alterna modo admin de claims: acceso total y salta todos los límites | op o permiso |
| `/oclaims moderator-mode` ✅ | Alterna modo moderador (muestra nombres de dueños en claims de party) | op o permiso |
| `/oclaims non-ally-mode` ✅ | Te ve/accede a tus claims como lo haría un no aliado (sirve para probar tu protección) | todos |
| `/oclaims impersonate [<jugador>]` ✅ | Actúas «como» otro jugador en comandos de claims; sin argumento, lo desactiva | op o permiso |

> **Útil para probar tu protección sin otro jugador:** `/oclaims non-ally-mode` y luego intenta romper un bloque en tu zona.

---

## 5. Parties: `/oparties` y `/opm` ✅

```
/oparties create                          # crear party
/oparties about                           # info de tu party
/oparties about <jugador-online>          # info de la party de otro jugador
/oparties about <perfil>                  # (op) igual, para cualquier perfil
/oparties member invite <jugador>         # invitar (jugador online)
/oparties join <id>                       # unirte a una party a la que te invitaron
/oparties member kick <nombre>            # expulsar o cancelar invitación
/oparties member rank <rango> <nombre>    # asignar rango: MEMBER | CLAIMER | MODERATOR | ADMIN
/oparties ally add <jugador>              # aliar la party de ese jugador
/oparties ally remove <dueño>             # quitar alianza (nombre del dueño de esa party)
/oparties transfer <nuevo-dueño> confirm  # transferir la propiedad de la party
/oparties leave                           # salir (el dueño no puede)
/oparties destroy confirm                 # destruir la party
/oparties chat <mensaje>                  # chat de party
/opm <mensaje>                            # atajo del chat de party
/oparties admin-mode                      # (op) ver chat de todas las parties
/oparties impersonate [<perfil>]          # (op) actuar como otro jugador en comandos de party
```

- Rangos de menor a mayor: `MEMBER` < `CLAIMER` < `MODERATOR` < `ADMIN` (el dueño está por encima).
- ⚠️ En tu servidor `partyOwnedClaims = false`: las parties existen, pero los claims siguen siendo personales.

---

## 6. Configuración de jugador: `/opac`

Comando para ver y cambiar opciones de claims/protección sin la interfaz gráfica.

### 6.1 Tipos de config (prefijo) ✅

| Prefijo | Qué configura |
|---|---|
| `player-config` | **Tu** config personal (el que usarás tú) |
| `default-player-config` | Valores por defecto de todos los jugadores |
| `party-claims-config` | Config de claims de party |
| `server-claims-config` | Config de claims de servidor |
| `expired-claims-config` | Config de claims caducados |
| `wilderness-config` | Config de la zona sin reclamar |

⚠️ Los prefijos distintos de `player-config` normalmente requieren ser op (no confirmado qué permisos exige cada uno).

### 6.2 Sintaxis ✅

```
/opac player-config get   <clave>                 # ver valor actual
/opac player-config help  <clave>                 # ayuda de esa opción
/opac player-config set   <clave> <valor>         # cambiar
/opac player-config reset <clave>                 # volver al valor por defecto
/opac player-config append <clave> <valor>        # añadir a opciones tipo lista (si la opción lo soporta)
/opac player-config deduct <clave> <valor>        # quitar de opciones tipo lista

# Sub-configuraciones (variantes de config para ciertos claims)
/opac player-config sub create <sub-id>
/opac player-config sub delete <sub-id>
/opac player-config sub list <posición-inicial>
/opac player-config sub <sub-id> get|help|set|reset|append|deduct <clave> [valor]

# Grupos de jugadores personalizados
/opac player-config player-groups <group-id> ...   # ⚠️ subcomandos: create, delete [confirm], include-player, exclude-player, include-group, exclude-group (confirmar con Tab)

# Operar sobre otro jugador (solo op)
/opac player-config for <jugador> set <clave> <valor>
```

- Las claves se autocompletan con Tab.
- ✅ **Formato de los valores de grupo:** en el juego se muestran como `Nobody`, `Party`, `Allies`, `Everyone`; en los archivos `.toml` se escriben `"N"`, `"P"`, `"A"`, `"E"`. Las opciones sí/no son `true`/`false`.
- ✅ **Qué puede cambiar cada jugador lo decide el servidor:** si una opción no está en `playerConfigurablePlayerConfigOptions` ni en `opConfigurablePlayerConfigOptions` (`openpartiesandclaims-server.toml`), todos usan el valor de la config por defecto y nadie puede cambiarla. Vaciando la primera lista (`= []`) los jugadores quedan sin config propia (ver sección 10).
- ✅ ID de sub-config: máx. N caracteres (lo define el servidor), solo letras A-Z, números, `-` y `_`.
- ✅ Borrar una sub-config convierte sus claims a la config principal (`main`).
- ✅ Algunas opciones están **forzadas por el servidor** (mensaje «The server config forces it to…») o **solo las puede cambiar un op** («Only server OPs can configure this option for you!»).

### 6.3 Claves de `player-config` (para `get`/`set`)

**Claims y límites**

| Clave | Significado |
|---|---|
| `claims.name` / `claims.color` | Nombre y color de tu zona |
| `claims.usedSub` | Sub-config usada en nuevos claims |
| `claims.bonusChunkClaims` / `claims.bonusChunkForceloads` | Bonus de claims / forceloads (normalmente solo op) |
| `claims.bonusAnchors` / `claims.bonusAnchorRange` | Bonus de anclas y rango |
| `claims.protectClaimedChunks` | Activar/desactivar protección de tus chunks |
| `claims.forceload.enabled` / `claims.forceload.offlineForceload` | Forceload y forceload sin conexión |
| `claims.wholePartyCanClaim` | Toda la party puede reclamar |
| `claims.protection.exceptions.reclaimable` | Quién puede reclamar encima |

**Excepciones de protección más usadas** (todas bajo `claims.protection.exceptions.`)

| Clave | Permite… |
|---|---|
| `fullAccess` | Acceso completo al chunk (para el grupo indicado) |
| `blocksByPlayers` / `blocksByMobs` / `blocksByExplosions` / `blocksByOther` | Modificar bloques por jugadores / mobs / explosiones / otros |
| `fireSpread` | Propagación de fuego |
| `cropTrample` | Pisotear cultivos |
| `entitiesByPlayers` / `entitiesByMobs` / `entitiesByExplosions` / `entitiesByFire` | Dañar entidades (granjas, animales) |
| `playersByPlayers` / `playersByMobs` / `playersByOther` | Daño a jugadores (PvP, mobs) |
| `raids` | Asaltos de aldea |
| `itemUse` | Uso de ítems |
| `itemPickupPlayers` / `itemTossPlayers` / `xpPickup` | Recoger/tirar ítems y XP |
| `mobLoot` | Botín de mobs |
| `naturalSpawnHostile` / `naturalSpawnFriendly` | Aparición natural de mobs |
| `spawnersHostile` / `spawnersFriendly` | Spawners |
| `chorusFruitTeleport` | Teletransporte con fruta de chorus |
| `netherPortalsPlayers` / `netherPortalsMobs` / `netherPortalsOther` | Portales del Nether |
| `buttonsByProjectiles` / `targetsByProjectiles` / `platesBy…` / `tripwireBy…` | Redstone (botones, placas, hilos) |

**Barreras y otros** (bajo `claims.protection.`): `fluidBarrier`, `pistonBarrier`, `dispenserBarrier`, `neighborChunksItemUse`, `playerDeathLoot`, `overrideMobGriefingRule`.

**Grupos por acción** (`claims.protection.exceptions.groups.<tipo>.<acción>`): configuran **qué grupo** (`nobody` / `party` / `allies` / `everyone` o grupos propios) puede hacer cada acción, p. ej. `groups.block.interact`, `groups.block.break`, `groups.entity.interact`, `groups.item.interact`.

**Party y ubicación:** `parties.name`, `parties.shareLocationWithParty`, `parties.shareLocationWithMutualAllyParties`, `parties.receiveLocationsFromParty`, `parties.receiveLocationsFromMutualAllyParties`.

**Grupos de jugadores:** `customPlayerGroups`, `bonusPlayerGroups`, `bonusPlayerGroupSpace`.

> Para ver las claves exactas y sus valores permitidos en tu servidor: escribe `/opac player-config get ` y pulsa Tab, o usa `/opac player-config help <clave>`.

---

## 7. Mensajes de error frecuentes y qué hacer

| Mensaje (resumido) | Causa | Qué hacer |
|---|---|---|
| «target chunk(s) too far away» | El área queda lejos de tu posición | Acércate al centro del área. `anyway` solo lo puede usar un op |
| «can't claim chunks from a different dimension» | Estás en otra dimensión | Ve a la dimensión correcta |
| «Too many chunks for a single action» | Área > `maxSingleClaimActionSize` | Divide el área en trozos o sube el valor en el config (con el servidor parado) |
| «You have reached your chunk claim limit» | Alcanzaste `maxPlayerClaims` | Libera chunks o sube el límite |
| «already claimed» / «already claimed by X» | Chunk ocupado | Elige otra zona |
| «dimension is not claimable» | Dimensión excluida en `claimableDimensions…` | Revisar el config del servidor |
| «claim task currently in progress» | Hay otra tarea de área activa | Espera o pulsa [INTERRUPT] |
| «You need to claim the target chunk(s) first» | Forceload sobre chunks no reclamados | Reclama primero |
| «reached your chunk forceload limit» | `maxPlayerClaimForceloads` | Quita forceloads |
| «chunk claims feature is disabled» | `claimsEnabled = false` | Activar en el config |
| «not within a claiming anchor's range» | Anclas activas | Coloca un ancla con `/oclaims anchor` |
| «don't have permission to make server claims» | Modo `server` sin permiso | Usa modo `player` |

---

## 8. Opciones del config del servidor relacionadas (`openpartiesandclaims-server.toml`)

| Opción | Efecto | Tu valor actual |
|---|---|---|
| `maxPlayerClaims` | Máx. chunks reclamados por jugador | 100 |
| `maxSingleClaimActionSize` | Máx. chunks en una acción (claim/unclaim/forceload) | 100 |
| `maxClaimDistance` | Distancia máxima (en X o Z, forma cuadrada) a la que reclamar | 5 |
| `maxPlayerClaimForceloads` | Máx. chunks con forceload | 5 |
| `partyOwnedClaims` | Claims de party en lugar de personales | false |
| `enabled` (en `[serverConfig.claims]`) | Activa todo el sistema de claims | true |
| `anchorBasedClaiming` | Exige anclas para reclamar (desde 0.32.x) | false |
| `maxPlayerClaimingAnchors` | Máx. anclas por jugador en una dimensión ✅ | 3 |
| `claimingAnchorRange` | Distancia máx. desde un ancla a la que se puede reclamar (en chunks ✅) | 16 |
| `claimingAnchorBonusPerPartyMember` / `claimingAnchorBonusForPartyOwner` | Con `partyOwnedClaims = true`: anclas extra por miembro de la party (0.1 = 1 ancla por cada 10 miembros ✅) y bonus fijo para el dueño si la party tiene otro miembro ✅. Sin efecto con `partyOwnedClaims = false` | 0.1 / 0 |
| `maxPlayerClaimingAnchorsPermission` / `claimingAnchorRangePermission` | Permisos para variar esos límites | `xaero.pac_max_claiming_anchors` / `xaero.pac_claiming_anchor_range` |
| `allowTouchingClaims` | Si es `false`, nadie puede reclamar chunks pegados al claim de otro (deja un chunk libre alrededor). Solo afecta a zona sin reclamar | false |
| `claimsSynchronization` | Envía los claims a los clientes (para mapas). `NOT_SYNCED`, `OWNED_ONLY`, `ALL` | NOT_SYNCED |
| `claimWelcomeMessages` | Mensaje al entrar/salir de un claim; útil para ver los bordes sin mapa | true |
| `playerClaimsExpirationCheckInterval` | Cada cuántos **minutos** se buscan claims caducados (múltiplos de 10) | 360 |
| `playerClaimsConvertExpiredClaims` | `false` = los claims caducados se liberan del todo | false |
| `playerConfigurablePlayerConfigOptions` | Opciones que el jugador puede cambiar con `/opac` | `[]` (vaciada, ver sección 10) |
| `opConfigurablePlayerConfigOptions` | Opciones extra que solo un op puede cambiar por jugador | bonus de claims/forceloads/grupos |
| `playerClaimsExpirationTime` | Horas de inactividad hasta que caducan tus claims | 8760 (365 días) |

Recuerda: editar el archivo **con el servidor detenido** (el mod puede sobrescribirlo al apagarse).

---

## 9. Pendiente de confirmar en el juego (Tab)

1. `/oclaims interrupt ` → ¿ofrece `for <jugador>`?
2. `/oclaims sub-claim ` → confirmar `current` y `use`.
3. `/opac player-config player-groups ` → ver subcomandos exactos.
4. `/oclaims claim ` con Tab en 0.32.7 → confirmar que `from` y `to` son obligatorios juntos (según el código).
5. Distancia máxima real (`maxClaimDistance`): unidad y forma de medirla.
6. Si un jugador ajeno puede **colocar** bloques en un claim con `itemUse = "N"` y `blocksByPlayers = "N"` (se espera que no).
7. Protección de entidades: `friendlyChunkProtectedEntityListType` y `hostileChunkProtectedEntityListType` están en `"ONLY"` con lista vacía, mientras el comentario del archivo dice que el valor por defecto es `ALL_BUT`. Efecto real sobre animales y mobs sin confirmar.

---

## 10. Configuración aplicada en este servidor (protección por defecto)

**Objetivo:** los jugadores solo reclaman chunks; no tienen config propia. Dentro de un claim, un jugador ajeno puede interactuar con todo **excepto romper bloques**, y los mobs (creepers, etc.) pueden dañar bloques como en vanilla.

### 10.1 Dónde están los archivos

| Archivo | Para qué |
|---|---|
| `/home/ec2-user/minecraft_server/config/openpartiesandclaims-server.toml` | Límites, caducidad y qué opciones puede cambiar cada jugador |
| `/home/ec2-user/minecraft_server/world/serverconfig/openpartiesandclaims-default-player-config.toml` | **Protección por defecto** de todos los claims (la copia en uso) |
| `/home/ec2-user/minecraft_server/defaultconfigs/openpartiesandclaims-*.toml` | ⚠️ Plantillas para mundos nuevos; no se confirmó que afecten al mundo actual |
| `.../world/serverconfig/openpartiesandclaims-{server-claim,expired-claim,wilderness}-config.toml` | Config de claims de servidor, caducados y zona sin reclamar (no tocados) |

Editar siempre con el servidor detenido (`sudo systemctl stop minecraft.service`).

### 10.2 Cambios hechos

En `default-player-config.toml`:

| Opción | Antes | Ahora | Efecto |
|---|---|---|---|
| `groups.block.interact.*` (20 entradas: Controls, Doors, Chests, Barrels, Ender_Chests, Shulker_Boxes, Furnaces, Hoppers, Dispenser-like, Anvils, Stonecutters, Grindstones, Cartography_Tables, Lecterns, Smithing_Tables, Looms, Jukeboxes, Beds, Beacons, Enchanting_Tables) | `"N"` | `"E"` | Cualquiera usa cofres, puertas, hornos, camas, etc. |
| `buttonsByProjectiles`, `targetsByProjectiles` | `"N"` | `"E"` | Botones y bloques objetivo con proyectiles |
| `blocksByMobs` | `"N"` | `"E"` | Los mobs (creeper, enderman…) pueden alterar bloques |
| `blocksByExplosions` | `false` | `true` | Los bloques pueden explotar |

En `openpartiesandclaims-server.toml`:

| Opción | Antes | Ahora |
|---|---|---|
| `playerConfigurablePlayerConfigOptions` (línea 43) | lista de ~90 opciones | `[]` |

### 10.3 Valores que se dejaron a propósito

| Opción | Valor | Por qué |
|---|---|---|
| `blocksByPlayers` | `"N"` | Nadie ajeno rompe bloques (el objetivo) |
| `itemUse` | `"N"` | El comentario del archivo dice que colocar bloques depende también de esta opción; con `"N"` los ajenos no colocan bloques. Con `"E"` podrían usar cubos de lava y mecheros |
| `fireSpread`, `cropTrample`, `raids`, `entitiesByExplosions`, `*ByMobs` (placas, hilos, entidades) | sin cambios | No se revisaron; ver sus comentarios antes de tocarlas |

### 10.4 Lo aprendido de los comentarios del archivo

- ✅ Las explosiones de creeper dependen de **dos** opciones: `blocksByExplosions` y `blocksByMobs`.
- ✅ `overrideMobGriefingRule = true`: los mobs siguen las opciones del mod en lugar de la regla `mobGriefing` de la partida.
- ✅ Con `fullAccess = "P"`, solo tu party tiene acceso total.
- ✅ Los cofres en `"E"` permiten a cualquiera llevarse su contenido.

### 10.5 Cómo revertir un cambio

Es el valor inverso en la misma línea: `"E"` → `"N"`, `true` → `false`. La lista `playerConfigurablePlayerConfigOptions` se restaura copiando el contenido original (no hay copia automática; se guardó en la conversación).

### 10.6 Cómo comprobar

- `/oclaims non-ally-mode` dentro de tu zona: te ves como un no aliado.
- `/opac player-config get <clave>` muestra el valor efectivo de una opción.
- Resultado reportado tras los cambios: el comportamiento en el juego fue el esperado.

---

## 11. Instantánea de la configuración actual (11-oct-2026)

Reconstruida a partir de las salidas pegadas durante la sesión. Tras actualizar a **0.32.7** se comparó el `server.toml` y la config por defecto con la copia anterior: no hay otros cambios que las opciones nuevas de anclas y la versión del formato (ver sección 12.3). **⚠️ Cambio = valor distinto al original o al predeterminado conocido.** Si editas algo, actualiza esta sección.

Para regenerarla desde el servidor (solo lectura):

```bash
S=/home/ec2-user/minecraft_server
grep -nvE '^\s*(#|$)' $S/config/openpartiesandclaims-server.toml | cut -c1-200
grep -nvE '^\s*(#|$)' $S/world/serverconfig/openpartiesandclaims-default-player-config.toml | cut -c1-200
```

### 11.1 `config/openpartiesandclaims-server.toml`

**General (`[serverConfig]`)**

| Opción | Valor |
|---|---|
| `defaultLanguage` | `"en_us"` |
| `autosaveInterval` | 10 |
| `playerSubConfigLimit` | 64 |
| `permissionSystem` | `"permission_api"` |
| `primaryPartySystem` | `"default"` |
| `maxPlayerGroups` / `playerGroupSpace` | 16 / 256 |
| `maxPlayerGroupsPermission` / `playerGroupSpacePermission` | `xaero.pac_max_player_groups` / `xaero.pac_player_group_space` |
| `playerConfigurablePlayerConfigOptions` | **`[]`** ⚠️ Cambio (antes ~90 opciones) |
| `opConfigurablePlayerConfigOptions` | `["claims.bonusChunkClaims", "claims.bonusChunkForceloads", "bonusPlayerGroups", "bonusPlayerGroupSpace"]` |

**Parties (`[serverConfig.parties]`)**

| Opción | Valor |
|---|---|
| `enabled` | true |
| `maxPartyMembers` / `maxPartyAllies` / `maxPartyInvites` | 64 / 64 / 16 |
| `partyExpirationTime` / `partyExpirationCheckInterval` | 720 / 360 |
| `partyChatLogging` | true |
| `adminModePermission` / `impersonationPermission` | `xaero.pac_parties_admin_mode` / `xaero.pac_parties_impersonation` |

**Claims (`[serverConfig.claims]`)**

| Opción | Valor |
|---|---|
| `enabled` | true |
| `partyOwnedClaims` | false |
| `claimBonusPerPartyMember` / `forceloadBonusPerPartyMember` | 100 / 2 (sin efecto con `partyOwnedClaims = false`) |
| `claimBonusForPartyOwner` / `forceloadBonusForPartyOwner` | 0 / 0 |
| `allowTouchingClaims` | false |
| `overLimitClaimAccessCooldown` | 5 (minutos) |
| `playerClaimsExpirationTime` | 8760 (horas) |
| `playerClaimsExpirationCheckInterval` | 360 (minutos) |
| `playerClaimsConvertExpiredClaims` | false |
| `maxPlayerClaims` | **100** ⚠️ Cambio (antes 20; predeterminado del mod 500) |
| `maxPlayerClaimForceloads` | 5 |
| `maxSingleClaimActionSize` | **100** (predeterminado 121 según tus notas) |
| `maxClaimDistance` | 5 |
| `maxPlayerClaimsPermission` / `maxPlayerClaimForceloadsPermission` | `xaero.pac_max_claims` / `xaero.pac_max_forceloads` |
| `serverClaimPermission` / `moderatorModePermission` / `adminModePermission` / `impersonationPermission` / `teleportationPermission` | `xaero.pac_server_claims` / `xaero.pac_claims_moderator_mode` / `xaero.pac_claims_admin_mode` / `xaero.pac_claims_impersonation` / `xaero.pac_claims_teleport` |
| `claimableDimensionsListType` / `claimableDimensionsList` | `"ALL_BUT"` / `[]` (todas reclamables) |
| `allowExistingClaimsInUnclaimableDimensions` / `allowExistingForceloadsInUnclaimableDimensions` | true / false |
| `claimsSynchronization` | `"NOT_SYNCED"` |
| `claimWelcomeMessages` | true |
| `anchorBasedClaiming` | false (nuevo en 0.32.7) |
| `maxPlayerClaimingAnchors` / `claimingAnchorRange` | 3 / 16 (nuevos) |
| `claimingAnchorBonusPerPartyMember` / `claimingAnchorBonusForPartyOwner` | 0.1 / 0 (nuevos) |
| `maxPlayerClaimingAnchorsPermission` / `claimingAnchorRangePermission` | `xaero.pac_max_claiming_anchors` / `xaero.pac_claiming_anchor_range` (nuevos) |

**Protección del servidor (`[serverConfig.claims.protection]`)** — sin cambios (valores del mod):

| Opción | Valor |
|---|---|
| `friendlyChunkProtectedEntityListType` / `hostileChunkProtectedEntityListType` | `"ONLY"` / `"ONLY"`, listas vacías ⚠️ difiere del `ALL_BUT` que indica el comentario (pendiente, sección 9) |
| `forcedBlockProtectionExceptionList` | `["minecraft:crafting_table"]` |
| `forcedEntityProtectionExceptionList` | `["minecraft:minecart"]` |
| `additionalBannedItemsList` | `["supplementaries:slingshot"]` |
| `itemUseProtectionExceptionList` | `["minecraft:firework_rocket"]` |
| `alwaysProtectBlocksFromEnchantments` | false |
| `reducedBoatEntityCollisions` | true |
| Listas vacías | `blockProtectionExceptionList`, `entityProtectionExceptionList`, `entityClaimBarrierList`, `forcedEntityClaimBarrierList`, `entitiesAllowedToGriefDroppedItems`, `nonBlockGriefingMobs`, `entityGriefingMobs`, `playerGriefingMobs`, `droppedItemGriefingMobs`, `completelyDisabled*Interactions` |
| Grupos opcionales (valores por defecto del mod) | `blockProtectionOptionalExceptionGroups` (20 grupos de bloques + `break$Crops`), `entityProtectionOptionalExceptionGroups` (Traders, Item_Frames, Livestock, Armor_Stands, Players), `entityClaimBarrierOptionalGroups` (Players), `blockAccessEntityGroups` (Villagers), `entityAccessEntityGroups` y `playerAccessEntityGroups` (Zombies), `droppedItemAccessEntityGroups` (Villagers, Piglins, Foxes), `itemUseProtectionOptionalExceptionGroups` (Books) |
| `entitiesAllowedToGrief*`, `entitiesAllowedToAccessPlayers` | Poción, tridente, flechas, perla de ender, huevo, bala de shulker (+ `minecraft:sheep` en `entitiesAllowedToGrief`) |
| `staticFakePlayers` / `staticFakePlayerClassExceptions` | `[IntegratedTunnels]` y un UUID / `TurtlePlayer` (ComputerCraft) |

### 11.2 `world/serverconfig/openpartiesandclaims-default-player-config.toml`

Es la protección que aplica a **todos** los claims. Grupos: `N` = Nobody, `P` = Party, `A` = Allies, `E` = Everyone.

**Generales**

| Opción | Valor |
|---|---|
| `claims.protectClaimedChunks` | true |
| `claims.wholePartyCanClaim` | false |
| `claims.bonusChunkClaims` / `claims.bonusChunkForceloads` | 0 / 0 |
| `claims.bonusAnchors` / `claims.bonusAnchorRange` | 0 / 0 (nuevas en 0.32.7; el archivo pasó de `version = 15` a `16`) |
| `claims.usedSub` / `usedServerSub` / `usedPartySub` | `"main"` |
| `claims.name` / `claims.color` | `""` / 0 |
| `claims.forceload.enabled` / `offlineForceload` | true / false |
| `protection.fluidBarrier` / `pistonBarrier` / `dispenserBarrier` | true / true / true |
| `protection.neighborChunksItemUse` | true |
| `protection.playerDeathLoot` | `"N"` |
| `protection.overrideMobGriefingRule` | true |
| `bonusPlayerGroups` / `bonusPlayerGroupSpace` | 0 / 0 |
| `parties.name` | `""` |
| `parties.shareLocationWithParty` / `shareLocationWithMutualAllyParties` | true / false |
| `parties.receiveLocationsFromParty` / `receiveLocationsFromMutualAllyParties` | true / false |

**Excepciones (`claims.protection.exceptions.`)**

| Opción | Valor | Nota |
|---|---|---|
| `fullAccess` | `"P"` | |
| `blocksByPlayers` | `"N"` | Nadie ajeno rompe bloques (objetivo) |
| `blocksByMobs` | **`"E"`** ⚠️ | Cambio (antes `"N"`) |
| `blocksByOther` | `"N"` | |
| `blocksRedirect` | true | |
| `blocksByExplosions` | **true** ⚠️ | Cambio (antes false) |
| `fireSpread` | false | |
| `blocksByEnchantments` | `"N"` | |
| `cropTrample` | false | |
| `buttonsByProjectiles` / `targetsByProjectiles` | **`"E"`** ⚠️ | Cambio (antes `"N"`) |
| `platesByPlayers` / `tripwireByPlayers` | `"E"` | |
| `platesByMobs` / `platesByOther` / `tripwireByMobs` / `tripwireByOther` | `"N"` | |
| `entitiesByPlayers` | `"E"` | |
| `entitiesByMobs` / `entitiesByOther` | `"N"` | |
| `entitiesRedirect` | true | |
| `entitiesByExplosions` / `entitiesByFire` | false / false | |
| `raids` | false | |
| `playersByPlayers` / `playersByMobs` / `playersByOther` | true / true / true | |
| `playersRedirect` | true | |
| `playerLightning` / `chorusFruitTeleport` | `"N"` / `"N"` | |
| `netherPortalsPlayers` | `"E"` | |
| `netherPortalsMobs` / `netherPortalsOther` | `"N"` | |
| `itemUse` | `"N"` | Dejado a propósito (sección 10.3) |
| `itemTossPlayers` / `itemTossMobs` / `itemTossOther` | `"E"` | |
| `itemTossRedirect` | true | |
| `mobLoot` / `xpPickup` | `"E"` | |
| `itemPickupPlayers` / `itemPickupMobs` | `"E"` | |
| `itemPickupRedirect` | false | |
| `naturalSpawnHostile` / `naturalSpawnFriendly` / `spawnersHostile` / `spawnersFriendly` | true (las cuatro) | |
| `projectileHitHostileSpawn` / `projectileHitFriendlySpawn` | `"N"` | |
| `reclaimable` | `"N"` | |

**Grupos por acción (`...exceptions.groups.`)**

| Grupo | Entradas y valor |
|---|---|
| `block.interact` | Las 20 entradas (Controls, Doors, Chests, Barrels, Ender_Chests, Shulker_Boxes, Furnaces, Hoppers, Dispenser-like, Anvils, Stonecutters, Grindstones, Cartography_Tables, Lecterns, Smithing_Tables, Looms, Jukeboxes, Beds, Beacons, Enchanting_Tables) = **`"E"`** ⚠️ Cambio (antes `"N"`) |
| `block.break` | `Crops = "N"` |
| `entity.interact` | `Traders`, `Armor_Stands`, `Players` = `"E"` |
| `entity.handInteract` | `Item_Frames = "E"` |
| `entity.break` | `Livestock = "E"` |
| `entity.blockAccess` | `Villagers = "E"` |
| `entity.entityAccess` / `entity.playerAccess` | `Zombies = "E"` |
| `entity.droppedItemAccess` | `Villagers`, `Piglins`, `Foxes` = `"E"` |
| `entity.barrier` | `Players = "E"` |
| `item.interact` | `Books = "E"` |

### 11.3 Archivos no revisados

`world/serverconfig/openpartiesandclaims-server-claim-config.toml`, `...-expired-claim-config.toml` y `...-wilderness-config.toml` (misma estructura que la config por defecto; no se modificaron a mano. Tras actualizar a 0.32.7 se verificó que pasaron a `version = 16` y que incluyen `bonusAnchors`; el resto de su contenido no se comparó). Los de `defaultconfigs/` son plantillas con fecha 22-sep; no se confirmó si afectan al mundo actual.

---

## 12. Operación del servidor y actualización del mod

### 12.1 Infraestructura (carpeta `/home/ec2-user/minecraft_services`)

| Elemento | Qué hace |
|---|---|
| `minecraft.service` (`/etc/systemd/system/`) | Arranca `run.sh` en `/home/ec2-user/minecraft_server`. Antes de arrancar borra `.empty-since` y ejecuta `set-jvm-heap.sh`. **Parada ordenada:** `ExecStop` envía `stop` por RCON (`rcon_client.py`) y espera hasta 60 s (`TimeoutStopSec=60`). `Restart=on-failure` con `RestartSec=10`: si el servidor falla al arrancar, se relanza cada 10 s hasta que se pare a mano |
| `check-players.timer` + `check-players.service` + `check-players.sh` | Cada 15 min consulta los jugadores por RCON. Si hay 0, crea `.empty-since`; si en la siguiente revisión sigue vacío, ejecuta `systemctl stop minecraft.service` y `shutdown -h now` (la instancia se **para**, no se destruye). Si no alcanza RCON (servidor caído o arrancando), no hace nada |
| `player-activity.service` / `player-activity-log.sh` | Activo; función no revisada |
| `caddy.service` / `Caddyfile` | Presentes; no revisados |

⚠️ La contraseña de RCON está en la unidad y en el script; no se reproduce aquí.

### 12.2 Procedimiento seguro para actualizar el mod

1. **Subir el JAR** al home del servidor con `scp -i "CLAVE.pem" "JAR" ec2-user@IP:/home/ec2-user/` (con el servidor encendido).
2. **Verificar el JAR:** `unzip -tq` sin errores; `unzip -p JAR META-INF/neoforge.mods.toml | grep -E '^version=|versionRange'` (el `versionRange` de NeoForge debe incluir tu versión, aquí 21.1.248).
3. **Parar el temporizador de autoapagado:** `sudo systemctl stop check-players.timer`. Si no, con el servidor vacío la instancia podría apagarse a mitad de la operación.
4. **Comprobar dependencias:** ningún otro mod debe mencionar `openpartiesandclaims` en su `neoforge.mods.toml` (en este servidor, solo el propio OPAC).
5. **Parar el servicio:** `sudo systemctl stop minecraft.service` (sin jugadores conectados); revisar con `journalctl -u minecraft.service -n 25` que terminó limpio.
6. **Sustituir el JAR** en `/home/ec2-user/minecraft_server/mods/`: mover el antiguo **fuera** de `mods/` (dos versiones a la vez no cargan) y poner el nuevo. Opcional: copiar antes `world/data/openpartiesandclaims`, `config/openpartiesandclaims-server.toml` y `world/serverconfig/openpartiesandclaims-*.toml` (aquí los datos de claims ocupan ~2 MB).
7. **Arrancar y vigilar:** `sudo systemctl start minecraft.service` (o reiniciar la instancia). Comprobar `Done (...)!` en el log y `NRestarts=0`; si entra en bucle, `sudo systemctl stop minecraft.service`.
8. **Comparar configs** con la copia anterior y revisar las opciones nuevas (el mod puede reescribir los archivos al arrancar). Comprobar sobre todo que `playerConfigurablePlayerConfigOptions` sigue en `[]`: el mod incluye un actualizador (`ServerConfigUpdater`) que, cuando sube la versión del formato de config de jugador, puede transformar esa lista y la de opciones configurables por ops. En la actualización a 0.32.7 no la modificó (verificado con `diff`), pero puede hacerlo en otra.
9. **Reactivar el temporizador:** tras reiniciar la instancia vuelve solo (`enabled`). Si solo se reinició el servicio: `sudo systemctl start check-players.timer`.
10. **Avisar a los jugadores:** el mod va en cliente y servidor; con versión distinta el cliente indica que está desactualizado y deben actualizarlo.
11. **Limpiar** las copias temporales.

### 12.3 Actualización realizada: 0.30.3 → 0.32.7

Hecha con un reinicio de la instancia (`sudo reboot`) en lugar de arrancar solo el servicio. El servidor arrancó (`Done (2.264s)!`) y el temporizador volvió `enabled` y `active`. Un cliente con la versión anterior fue rechazado; con el cliente actualizado conectó y funcionó.

| Qué | Resultado |
|---|---|
| `server.toml` | Opciones nuevas de anclas (sección 8). Tus valores se conservaron, incluido `playerConfigurablePlayerConfigOptions = []`. Los demás cambios que mostró `diff` son de formato (mismo texto en cada línea; causa exacta sin confirmar) |
| `default-player-config.toml` | `version` 15 → 16 y `bonusAnchors`/`bonusAnchorRange` = 0. Se conservaron tus ajustes de protección |
| `server-claim`, `expired-claim`, `wilderness` | Migrados a `version = 16` y con `bonusAnchors` (verificado en los cuatro configs de `world/serverconfig`). El resto de su contenido no se comparó (la copia anterior se eliminó) |
| `config/openpartiesandclaims-server-1.toml.bak` | Lo creó el mod/NeoForge al migrar el `server.toml` (no es una copia manual); **eliminado después**. El mensaje completo del aviso `ConfigTracker` no se leyó |
| Avisos en el log | `Reference map 'openpartiesandclaims.refmap.json' ... could not be read` (mixins). Probablemente inofensivo; no confirmado |
| Nombres de opciones: JAR vs archivos | Se compararon los nombres de opción del `ServerConfig` del JAR 0.32.7 y las claves de config de jugador del archivo de idioma con tus archivos: **las únicas opciones nuevas son las de anclas** (`anchorBasedClaiming`, `maxPlayerClaimingAnchors`, `claimingAnchorRange`, dos bonus por party, dos permisos, y `bonusAnchors`/`bonusAnchorRange` en la config de jugador). Limitación: un valor **por defecto** que haya cambiado en una opción ya existente no se detecta así, porque el mod no pisa los valores que ya están en el archivo |
| Comentarios de las opciones nuevas | Leídos y coherentes con lo documentado: las anclas están desactivadas (`anchorBasedClaiming = false`) y los valores nuevos no afectan al juego. Las listas `playerConfigurablePlayerConfigOptions = []` y `opConfigurablePlayerConfigOptions` (4 opciones de bonus) se conservaron |
| Copia temporal `opac-backup-0.30.3` | Eliminada tras la actualización (había un sistema de backup aparte) |
| Claims y datos de jugadores | Sin verificación explícita tras actualizar; comprobar con `/oclaims about` |

### 12.4 Comprobaciones recomendadas tras actualizar

- `/oclaims about`: debe mostrar tu número de claims.
- `/oclaims non-ally-mode` dentro de tu zona: abrir cofres y accionar mecanismos debe funcionar; romper bloques no.
- `/opac player-config set claims.protectClaimedChunks false` debe ser rechazado.
- Con Tab: `/oclaims claim ` → debe pedir `from` y `to` juntos.

---

## 13. Dar acceso a otra persona a tus claims (party)

**Cómo funciona en este servidor:** `fullAccess = "P"` en la config por defecto (`world/serverconfig/openpartiesandclaims-default-player-config.toml`). ✅ El comentario del archivo dice que el grupo elegido recibe acceso total a los chunks reclamados con esa config; `"P"` = jugadores de la misma party que el dueño. La opción la fija el servidor para todos (no es configurable por jugadores), así que **la única vía es una party**.

**Pasos** (el invitado debe estar conectado):

1. `/oparties create` (si no tienes party).
2. `/oparties member invite <jugador>`.
3. El invitado pulsa [ACCEPT] en el chat o escribe `/oparties join <id>`.
4. Quitar el acceso: `/oparties member kick <nombre>`.

**Limitaciones**

- Acceso a **todos** tus claims; no se puede limitar a unos chunks.
- Es **recíproco**: «la misma party que el dueño» vale en ambos sentidos, así que tendrás acceso total a los claims que él haga con la misma config.
- No da poderes de gestión: con `partyOwnedClaims = false` los claims siguen siendo personales y el invitado no puede reclamar ni liberar chunks a tu nombre.
- `fullAccess` no incluye a las parties aliadas (grupo `"A"`).
- ⚠️ El comentario no menciona rangos de party; si un miembro de rango básico tiene el mismo acceso que uno de rango alto no está confirmado.
- Límites de party en el server config: `maxPartyMembers = 64`, `maxPartyInvites = 16`.
- Alternativa sin party: no existe con esta configuración. Habría que añadir `claims.protection.exceptions.fullAccess` a una lista de opciones configurables, lo que contradice el objetivo de que los jugadores no tengan config propia.

**Prueba:** que el invitado abra un cofre y rompa un bloque dentro de tu zona; tras `kick`, que ya no pueda.
