# 8 BITS BATTLE

Juego multijugador de aula: todos luchan y solo puede quedar uno.

## Desarrollo local

Instala las dependencias una vez y arranca el servidor:

```bash
npm install
npm run dev
```

Abre `http://localhost:3000`. El primer navegador abierto desde el mismo equipo es el panel del profesor; los demás dispositivos de la red local entran con la IP que muestra la consola. `INICIAR.bat` sigue disponible como acceso directo para Windows, pero ejecuta el mismo servidor.

## Publicarlo para jugar desde cualquier red

El juego necesita conexiones WebSocket persistentes y una memoria de partida compartida. Por eso se despliega en dos servicios:

| Servicio | Dónde | Función |
| --- | --- | --- |
| Cliente (`public/`) | Vercel | URL pública, rápida y accesible por todos |
| `server.js` | Render Web Service | Partida, jugadores y WebSocket seguro (`wss://`) |

### 1. Servidor de partida en Render

1. En [Render](https://dashboard.render.com/), crea **New > Blueprint** y selecciona este repositorio de GitHub.
2. Render detectará `render.yaml`. Antes de crear el servicio, define una clave larga para `HOST_KEY` (guárdala: no se publica en GitHub).
3. Cuando termine, copia la URL del servicio, por ejemplo `https://eightbits-cj9p.onrender.com`.

### 2. Cliente público en Vercel

1. En `public/config.js`, sustituye el valor vacío por la URL segura del servicio Render:

```js
window.BATTLE_CONFIG = { websocketUrl: 'wss://eightbits-cj9p.onrender.com' };
```

2. Haz commit y push de ese cambio.
3. En [Vercel](https://vercel.com/new), importa `pericoperez/8BITS`. La configuración del repositorio ya indica que debe publicar `public/` y que el comando de desarrollo es `npm run dev`.
4. Comparte la URL `https://...vercel.app` que te entregue Vercel. Cualquier persona puede abrirla y jugar desde cualquier red.

### Abrir una partida como profesor

Comparte la URL de Vercel normal con el alumnado. El profesor abre la misma URL añadiendo la clave privada:

```text
https://tu-juego.vercel.app/?host=TU_HOST_KEY
```

No compartas esa versión del enlace: quien tenga la clave puede pulsar **EMPEZAR PARTIDA**. El alumnado solo necesita la URL limpia.

> En el plan gratuito de Render, el servidor se duerme tras 15 minutos sin tráfico. La primera conexión posterior puede tardar alrededor de un minuto; durante una partida activa los mensajes WebSocket lo mantienen despierto.

## Configuración del juego

Al principio de `server.js`: `MAX_SHOTS`, `MAX_HP`, `SPEED`, `ZONE_DELAY`, `PORT` y el mapa (`MAP`).
