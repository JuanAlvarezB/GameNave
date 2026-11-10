# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Idioma

Todo debe estar siempre en español: este archivo `CLAUDE.md`, las respuestas, los comentarios del código, los textos de la interfaz, los mensajes de commit y la documentación. Si se edita este archivo, debe seguir escrito en español.

## Control de versiones

Siempre que se vaya a subir un cambio a la rama principal, el usuario es quien ejecuta `git add`, `git commit` y `git push`. Claude no debe ejecutar estos comandos; solo edita los archivos y, si ayuda, sugiere un mensaje de commit en español.

## Proyecto

Clon de Asteroids en HTML5 Canvas y JavaScript puro. No hay build, bundler, `package.json`, linter ni tests. Todo el juego está en `game.js`, cargado desde `index.html`. El directorio no es un repositorio git.

## Cómo ejecutarlo

Abre `index.html` en el navegador, o sirve la carpeta con `npx serve .` y visita `http://localhost:3000`. Recarga la página para ver los cambios.

## Arquitectura (`game.js`)

Todo vive en un solo archivo con variables globales de módulo, en este orden: entrada, utilidades, clases de entidades, estado del juego, update, draw y loop principal.

### Mundo y entrada

- **Mundo fijo de 800×600.** `W` y `H` están escritos a mano y deben coincidir con los atributos del `<canvas>` en `index.html`. El mundo es toroidal: todas las entidades se mueven con `wrap(v, max)`, excepto `Particle`, que no hace wrap.
- **Entrada.** `keys[code]` guarda las teclas mantenidas. `pressed(code)` es una comprobación de "recién pulsada" que **consume** la marca, así que debe llamarse una sola vez por frame y por tecla. El disparo y el reinicio la usan, por lo que mantener Espacio no dispara en automático.

### Entidades

- `Bullet`, `Asteroid`, `Ship` y `Particle` tienen `update(dt)` y `draw()`.
- Se marcan para eliminar con `dead = true` y el bucle de update reconstruye los arreglos con `filter`.
- `Asteroid.split()` devuelve los asteroides hijos, que se agregan después de la fase de colisiones.
- Las tablas `RADII`, `SPEEDS` y `POINTS` se indexan por tamaño (1 = pequeño, 2 = mediano, 3 = grande). El índice 0 no se usa. Los asteroides pequeños dan más puntos.

### Estado y flujo del juego

- **Máquina de estados.** La variable `state` vale `'playing' | 'dead' | 'gameover'`.
  - En `'dead'` corre un `deadTimer` de 2 segundos mientras asteroides y partículas siguen actualizándose. Luego la nave reaparece con `ship.reset()` (3 segundos de invencibilidad, dibujada parpadeando).
  - `'gameover'` espera Espacio para llamar a `initGame()`.
  - `nextLevel()` se ejecuta cuando `asteroids.length === 0` y crea `3 + level` asteroides grandes a al menos 130px del centro.
- **Colisiones.** Son comprobaciones de círculos con `dist` y el `radius` de cada entidad. En la colisión de la nave, el radio del asteroide se multiplica por 0.82 para ser más permisivo.
- **Loop.** `loop()` usa `requestAnimationFrame` con `dt` en segundos, limitado a 0.05 para evitar saltos enormes si la pestaña queda en segundo plano. No usa timestep fijo.

## Notas

- El README menciona power-ups y un asteroide tipo "estrella fugaz", pero ninguno existe en `game.js`.

## Testing
 - Para crear. diseñar y ejecutar las pruebas ir al archivo @ArquitecturaTesting.md
