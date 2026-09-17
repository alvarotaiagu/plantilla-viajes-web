# Viajes Arroaz — plantilla de web para agencia de viajes

> **Sitio de demostración. Viajes Arroaz es una agencia ficticia**: el nombre,
> la dirección, los teléfonos, los seis viajes, sus precios, sus fechas y las
> notas del cuaderno son de muestra y no corresponden a ninguna agencia real.
> **No se publica ningún número de licencia de agencia de viajes**, porque no
> se inventa. La web lleva `noindex, nofollow` a propósito.

Demo: **https://alvarotaiagu.github.io/plantilla-viajes-web/**

Web estática de una sola página (más aviso legal, privacidad y 404). Sin
framework, sin build, sin backend y sin npm: se abre con doble clic. GSAP,
ScrollTrigger y Lenis entran por CDN; si el CDN cae, la página se lee entera.

---

## El concepto: «Sellos»

Lo que queda de un viaje bien hecho no es el folleto: es **el sello en el
pasaporte**. Así que cada viaje de la casa es un sello dibujado, con su código,
su fecha y su tinta, y **se estampa en la página cuando llegas a él**.

- El **hero** es una página de pasaporte con pauta, donde caen tres sellos en
  tres tintas distintas (verde de garita, mostaza y rojo).
- Los **seis viajes** son seis sellos distintos: círculo doble, rectángulo con
  marco, hexágono, escudo, círculo troquelado y cartela. Cada ficha lleva
  días, salida, plazas, precio desde y **qué incluye y qué no**.
- El golpe de sello es una **transición CSS con rebote sobre el propio `<svg>`**
  disparada por un `IntersectionObserver`. Ni un tween de GSAP sobre un `<g>`,
  que es donde el CSS y GSAP se pelean por el atributo `transform`. Por eso el
  sello se estampa igual sin CDN, y con movimiento reducido aparece ya puesto.

---

## Mapa de secciones

| # | Sección | Qué hace |
|---|---|---|
| — | Cabecera | Fija, se «posa» al bajar. Menú móvil a pantalla completa con `aria-expanded`. |
| 01 | Hero | Página de pasaporte con tres sellos que caen, titular char-reveal y tres cifras. |
| — | Cinta | Marquee infinito con la velocidad ligada a la del scroll. |
| 02 | Los viajes | **Seis sellos que se estampan**, con días, salida, plazas, precio y lo que incluye. |
| 03 | A medida | Los cuatro pasos, de la charla a la propuesta en 72 horas. |
| 04 | Lo que no hacemos | Vuelos sueltos, cruceros, catorce países en diez días… dicho en la web, no en la letra pequeña. |
| 05 | La agencia | Quiénes son, el aviso de la licencia y dos notas del cuaderno. |
| 06 | Contacto | Datos, estado del horario en vivo y mapa **solo bajo clic**. |
| — | Pie | Sello de demostración, horario, legal y créditos. |

---

## Qué hay que tocar para reskinearlo a un cliente real

1. **Datos de la agencia**: el bloque `application/ld+json` del `<head>`
   (schema.org `TravelAgency`), la sección `#contacto` y el `<footer>`. Quitar
   el sello de demostración y **quitar
   `<meta name="robots" content="noindex, nofollow">`**. El schema **no lleva
   `aggregateRating`**.
2. **La licencia**: el bloque `.licencia` de la sección «La agencia» y el
   apartado correspondiente del aviso legal. Ahí van el número XG-…, el seguro
   y la garantía frente a insolvencia. **No se rellena con un número
   inventado.**
3. **Los viajes**: cada `<article class="viaje">` lleva su sello en línea y su
   `<dl>`. Para un viaje nuevo se copia una ficha, se cambia el SVG del sello
   (o se reutiliza otra forma) y se le da su `--giro` en el `style`.
4. **Las tintas**: `--verde`, `--mostaza` y `--rojo` en `:root`. El reparto por
   tarjeta está en `.viaje:nth-child(2n)` y `:nth-child(3n)` de
   `css/estilo.css`.
5. **Horario**: el array `HORARIO` de `js/main.js` (minutos desde medianoche,
   domingo = índice 0) y el `<li>` de horario del HTML. De ahí sale el «abierto
   ahora».
6. **Paleta y tipografía**: `:root` de `css/estilo.css` y el `<link>` de Google
   Fonts.
7. **Fotos**: `assets/fotos/`, dos anchos (`-800`, `-1600`). **Mantener el pie
   «foto de archivo»** mientras no sean fotos reales de los viajes del cliente:
   enseñar una playa que no es la que vendes es el engaño más común del sector.
8. **Mapa**: la consulta del `iframe` en `js/main.js`. **No quitar el botón**.

---

## Comportamiento degradado y rendimiento

- **Sin GSAP** (CDN caído o JS bloqueado) la página se ve entera: `has-motion`
  solo se enciende si existen `gsap` y `ScrollTrigger`. Comprobado abortando el
  CDN: titular a opacidad 1, los tres sellos del hero puestos (opacidad 0,92),
  las seis fichas completas y el horario en vivo funcionando.
- **`prefers-reduced-motion: reduce`**: se apaga el movimiento, no el
  contenido. Comprobado llevando el quinto viaje al centro en esa pasada: la
  ficha se marca visible y el sello está estampado.
- **Tareas largas medidas** con `PerformanceObserver` (`longtask`): en la
  verificación local, **una sola tarea larga de 103 ms** al cargar (GSAP +
  webfonts), ninguna después. Queda en `window.__tareasLargas`.
- Accesibilidad: contraste AA, foco visible, saltar al contenido, cada sello
  con su `aria-label` (por ejemplo «Sello de São Miguel, Azores»), `alt` con
  sentido en las fotos y titulares partidos con su `aria-label`.
- **Capturas**: `screenshots/` trae el recorrido completo en escritorio y móvil
  más las pasadas sin GSAP y con movimiento reducido, en JPEG.

---

## Créditos

- Fotografías: Pexels, licencia libre, acreditadas en
  [`CREDITOS.md`](CREDITOS.md). Ninguna corresponde a los viajes descritos, y
  todas van rotuladas como foto de archivo.
- Sellos, página de pasaporte, logotipo, dibujo del mapa y `og:image`: hechos
  para esta plantilla.
- Tipografías: [Spectral](https://fonts.google.com/specimen/Spectral) y
  [Epilogue](https://fonts.google.com/specimen/Epilogue) (OFL).
- Movimiento: [GSAP](https://gsap.com) + ScrollTrigger y
  [Lenis](https://github.com/darkroomengineering/lenis), por CDN.

## Decisiones tomadas

- **Nombre comprobado antes de fijarlo**: se descartó «Viajes Ronsel» porque
  existe una agencia real con ese nombre en Pontevedra. «Arroaz» —el delfín, en
  gallego— no aparece como agencia.
- **Sin número de licencia inventado.** Es el dato que distingue a una agencia
  legal de una que no lo es, y está en un registro público.
- **Las fotos, rotuladas como de archivo.** Un banco de imágenes no puede dar
  la foto de un viaje que no existe, así que se dice.
- **Una sección entera para lo que no se hace.** En un sector donde todo el
  mundo lo vende todo, decir que no vendes vuelos sueltos ni cruceros es la
  forma más rápida de que te crean el resto.
- **Sin motor de reservas ni pasarela de pago**: dos personas y seis salidas al
  año no lo necesitan, y fingirlo daría una idea equivocada de lo que se
  entrega.
