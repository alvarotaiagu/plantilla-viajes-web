# Auditoría de accesibilidad — Viajes Arroaz

Hecha con **axe-core 4.x** inyectado en la página y ejecutado con
`axe.run(document, { runOnly: wcag2a, wcag2aa, wcag21a, wcag21aa, best-practice })`
desde Playwright (Chromium).

## Cómo se pasó

Cada pasada: carga la página, espera a la intro, **cierra el aviso de cookies**,
**recorre la página entera con la rueda** (26 × 900 px) para que todo lo que
aparece con `IntersectionObserver` —los seis sellos, las apariciones— esté ya
revelado, vuelve arriba e inyecta axe.

Pasadas: **portada en escritorio (1440×900) y en móvil (390×844)**, aviso legal
y 404 (escritorio y móvil).

## Resultado

| Pasada | Antes | Después |
|---|---|---|
| Portada · escritorio | 56 nodos de `color-contrast` | **0** |
| Portada · móvil | 56 de `color-contrast` + 1 de `button-name` | **0** |
| Aviso legal | 0 | **0** |
| 404 · escritorio y móvil | 0 | **0** |

## Qué estaba mal y cómo se arregló

Los contrastes se calcularon con un script sobre la fórmula WCAG, probando cada
token contra **el peor de los fondos sobre los que aparece**.

1. **El token de texto apagado, `--tinta-suave`, no llegaba a AA.** `#8b8177`
   daba 3,02 sobre el panel, 3,33 sobre el papel y 3,60 sobre la crema de las
   fichas. Es el color de la bajada de la marca, el antetítulo, los `dt` de las
   cifras, los `dt` de cada viaje, el «qué incluye» de cada ficha, los pies de
   foto y las notas.
   → **`--tinta-suave: #6c655d`**, calculado contra `--panel` (`#ece4d4`):
   **4,54** ahí, 5,01 sobre el papel y 5,41 sobre la crema.
2. **La tinta mostaza de los sellos daba 2,44 sobre el papel de las fichas.**
   Es la tinta de dos de los seis sellos y del sello del pasaporte del hero.
   Siguiendo la regla, **la mostaza de marca no se toca**: se añadió
   **`--mostaza-tinta: #80631c`** (**4,51** ya contando la opacidad del sello) para la tinta de los sellos sobre
   papel, y `--mostaza` se queda donde funciona —sobre el fondo oscuro del pie
   (6,30) y en los filetes—. También queda anotado **`--mostaza-clara`** para
   texto sobre tinta oscura, por si al reskinear hace falta.
   *Nota:* axe **no evalúa el contraste del texto dentro de SVG**, así que estos
   sellos no salían en el informe: se detectaron midiéndolos a mano con el mismo
   script. Es el punto ciego que hay que recordar en cualquier plantilla con
   dibujo propio.
3. **El botón del menú móvil se quedaba sin nombre accesible.** Por debajo de
   520 px se oculta la palabra «Menú» con `display: none` y el botón se quedaba
   con dos rayas `aria-hidden`.
   → `aria-label="Abrir el menú"` en el `<button>`.
4. **La etiqueta del cursor sobre su propia mancha**: la mancha pasa del 30 % al
   72 % de tinta para que el texto crema se lea de verdad.

## Lo que esta plantilla ya hacía bien

- **No apaga texto con `opacity`**: los secundarios eran ya tokens de color. El
  fallo estaba en el valor, no en la técnica.
- Cada sello lleva `role="img"` y su `aria-label` («Sello de São Miguel,
  Azores»), y el dato que contiene está además escrito como texto en la ficha,
  así que ningún lector depende del dibujo.
- Landmarks, saltar al contenido, foco visible, `aria-expanded` en el menú,
  `alt` con sentido y titulares partidos con su `aria-label`.

## Lo que axe no mira, y aquí se ha mirado a mano

- **Texto dentro de SVG** (los códigos y fechas de los nueve sellos). Medidos
  con el script: verde `#1f4e4a` 8,81 · rojo `#a63d2f` 5,95 · mostaza corregida
  `#80631c` 4,51, todos sobre la crema de las fichas y **midiendo el color
  efectivo tras el `opacity: .92` del sello**.
- **La opacidad del sello** (`.sello { opacity: .92 }`) es una licencia de tinta
  gastada. Como el pliego avisa, una opacidad sobre texto no la ve la
  herramienta: aquí se ha resuelto **calculando el color efectivo tras la
  mezcla** (0,92 de tinta + 0,08 de papel) y eligiendo el token para que sea el
  resultado el que pasa AA, no el color declarado. Con la mostaza anterior el
  declarado daba 4,54 y el efectivo 4,02: por eso bajó a `#80631c`.
