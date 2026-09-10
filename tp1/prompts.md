# Prompts — TP 1

El registro del proceso, en orden. Iteración de prompts en una sola conversación de Gemini Canvas.

---

## 1 — Prompt inicial

```text

Quiero diseñar intencionalmente un frontend con una UI mala. Construye un sitio de una página
para aprender idiomas. Que el <header> contenga el nombre del sitio, "Multilingo", y una frase
que diga "Selecciona que idioma quieres aprender". Que el color de fondo sea --bg #7ac70c.
Que el <main> contenga botones <button> con las opciones de idioma (chino, ruso, arabe, japonés,
coreano, hebreo, español, alemán, francés, portugués). Ahora viene la gracia: que los botones
estén en grid, con border y margin 0px, que no se sombreen ni cambien de color al seleccionarse,
y que todos los botones y el sitio estén en el idioma seleccionado (cosa que no se pueda distinguir
cual es el botón de español, cual el de inglés, etc. Que los botones no tengan banderas tampoco).
Para confirmar el cambio, que haya tres botones <button> en el <footer>, en este orden: aceptar,
volver al anterior, cancelar. Que sean del mismo color #f0f0f0, y que no tengan simbolos que ayuden
a entender que son. Que sea una página en un solo archivo HTML, con CSS y JS inline, vanilla JS,
sin dependencias externas. 

```

**Qué intentaba lograr:** la estructura del sitio y sus componentes y comportamientos principales sobre los cuales ir iterando, con indicaciones para que la experiencia fuese frustante.

**Qué devolvió:** la estructura principal del sitio: header, main y footer, con los botones de opciones de idioma y confirmación, con los colores deseados, pero con el idioma por defecto en español, las opciones de idioma separadas y distinguibles y con el DOM actualizandose al seleccionar la opción de idioma. 

**Qué hice con eso:** lo testee en el Gemini Canvas. Al verificar la funcionalidad de los botones y el cambio de idioma, seguí creando prompts para entorpecer la experiencia.

---

## 2 — Iteración para entorpecer la identifiación de los botones y forzado de idioma inicial a chino

```text

Bien, pero que el padding de los <button> de los idiomas tambien sea 0px, cosa
que esten bien comprimidos y parezcan que son parte de la misma oración, que no
se pueda distinguir que son palabras separadas. Que el idioma inicial del sitio
sea chino (state currentLanguage: 'zh'). Que el DOM no se actualice hasta presionar
el botón aceptar (el botón aceptar es para cambiar de idioma, no para "avanzar" a
la siguiente pagina). 


```

**Qué intentaba lograr:** Que el usuario no entienda de que era la pagina al principio, y que no sepa que habia botones ni que sucedia al presionarlos.

**Qué devolvió:** el sitio con el idioma inicial en chino, pero con los botones aún separados (se logra interpretar que son opciones, pero no de que)

**Qué hice con eso:** lo testee en el Gemini Canvas. Verifiqué que al seleccionar las opciones, estas no hacian nada hasta apretar el botón de aceptar. Luego seguí creando prompts para dificultar la identificación de las opciones

---

## 3 — Segunda iteración para entorpecer la identifiación de los botones

```text

Creo que el layout grid hace que no logre mi proposito de que las opciones esten pegadas.
Que otra opción tengo para lograr esto?


```

**Qué intentaba lograr:** Que las opciones estén pegadas una al lado de la otra, de manera que no puedan distinguirse y parezcan texto de una misma oración

**Qué devolvió:** el sitio con las opciones ordenadas mediante un layout flex-wrap: wrap, logrando el efecto deseado.

**Qué hice con eso:** lo testee en el Gemini Canvas. Verifiqué que ahora las opciones estaban pegadas y que seguian siendo funcionales. Descubrí que la opción "volver al anterior" no hacía nada asi que generé mas prompts para arreglar esto.

---

## 4 — Fix para botón volver hacia atrás

```text

Falta un evento para el <button> "volver al anterior". Haz que al hacer click en este,
el DOM se actualice a un estado previousLanguage


```

**Qué intentaba lograr:** Que en medio de la confusión, exista un botón que aleje mas al usuario de llegar al idioma deseado.

**Qué devolvió:** el sitio con las opcion de volver al idioma anterior funcionando correctamente.

**Qué hice con eso:** lo testee en el Gemini Canvas. Verifiqué que ahora las opcion efectivamente revertia al idioma previo.

---

## 5 — Agregado de sonido y control de volumen tosco

```text

Ahora, haz que al presionar cada botón, se escuche un tono de 800Hz de 1 segundo de duración.
Agrega un <article> que diga "volumen", que dentro contenga dos <buttons>, "mas" y "menos"
(en ese orden, y escrito, no con simbolos + y -). Que el estado count inicial del volumen sea
el máximo posible (100), cuando se haga clic en los buttons "mas" o "menos", haz que el count
aumente o disminuya de a 1. 

```

**Qué intentaba lograr:** Que cada clic en un botón produzca un sonido molesto, y que para bajar el volumen se deba presionar botones que lo reducían de a saltos muy pequeños, forzando al usuario a presionarlo varias veces

**Qué devolvió:** el sitio igual a la versión previa, con un error [GLOBAL] TypeError: Cannot read properties of null (reading 'addEventListener')

**Qué hice con eso:** le pase el código de error al agente para que lo resuelva

---

## 6 — Fix de implementación de sonido control de volumen

```text

obtengo el error [GLOBAL] TypeError: Cannot read properties of null (reading 'addEventListener')

at blob:https://0vs48jmcqme6tk28zyyc17mhi4itdb8tleuihlnvnyj7arw37z-h974493514.scf.usercontent.goog/64b5681f-a80d-4b42-93a4-ed5112eb7daa:854:43

y no veo el article de volumen 

```
**Qué intentaba lograr:** Arreglar la implementación del sonido y el control de volumen

**Qué devolvió:** el sitio emitiendo sonido por cada botón presionado y el article con el control de volumen funcionando como se pretendía, pero manteniendose el article en español

**Qué hice con eso:** testee en Gemini Canvas que todos los botones emitan sonido, y que el control de volumen funcionase de a saltos molestamente pequeños como se pretendia. Generé otro prompt para cambiar el idioma del control del volumen para perder más al usuario entre tantas opciones.

## 7 — Cambio de idioma del control de volumen

```text

perfecto, solo falta que el article del volumen y sus botones tambien esten en el currentLangauge

```
**Qué intentaba lograr:** Perder más al usuario con mas opciones

**Qué devolvió:** el sitio completo funcionando como se pretendía, con el article de volumen en el idioma del resto del sitio.

**Qué hice con eso:** testee en Gemini Canvas todas las funcionalidades implementadas, le di el visto bueno y di por concluido el trabajo.

## Conversación completa
Una sola conversación de Gemini Canvas, sin reiniciar el hilo. El artefacto final tiene 320 líneas en un archivo.



