# TP 1 — Sitio de aprendizaje de idiomas

Una parodia de un popular sitio y aplicación de aprendizaje de idiomas, con la peor UI posible.
## Cómo se ejecuta

Doble click en `index.html`. Un solo archivo, sin dependencias.

## Qué me propuse construir

Una página para seleccionar qué idioma se desea aprender, pero con la peor UI posible: el sitio arranca en un idioma fijo que por probabilidad no conocés (chino). No se puede distinguir cuáles son los botones para seleccionar el idioma. Los botones de cancelar, aceptar y volver atrás están ordenados de manera poco habitual. También se emite un sonido cada vez que se presiona cualquier botón, y para bajarle el volumen se debe presionar varias veces el botón de disminuir el volumen, el cual también es difícil de identificar a la primera.

## Decisiones que tomé yo

**Idioma inicial extraño:** Lo más importante, solicite que el idioma inicial de la página esté seteado en una variable currentLanguage, y que el idioma inicial sea chino, el cual por probabilidad el usuario no conoce, y por ende no logrará entender que esta viendo.

**Layout flexbox y opciones sin distintivos:** Para que las opciones de idioma fueran ilegibles, anulé el padding y el margin de los botones  y utilicé un layout con display: flex y flex-wrap: wrap. Esto hizo que los botones colapsaran y se comportaran como una sola palabra continua en la pantalla. Además, forcé a que las opciones cambién con el idioma actual del sitio y no posean banderas, para que no se pueda distinguir cual es cual.

**Desacoplamiento entre Estado y DOM:** Cuando el usuario hace clic en un idioma, solo se actualiza la variable pendingLanguage en el estado de JS, sin mutar el DOM. El usuario no recibe ningún feedback visual. El DOM recién se actualiza al presionar "Aceptar", forzando al usuario a confirmar a ciegas.

**Botón “Volver al anterior:** Agregué una variable previousLanguage al estado. Mientras el usuario intenta adivinar cual opción es aceptar, si presiona el botón "Volver al anterior", simplemente se revierte la mutación del DOM al idioma incomprensible previo, creando un bucle de frustración.

**Control de volumen de a 1 punto:** Agregué sonido al presionar cualquier botón. El estado inicial del volumen al presionar botones es 100. Los botones de "más" y "menos" solo mutan el estado de a 1 punto. Por lo tanto, el usuario debe hacer 100 clics (y escuchar 100 pitidos) para silenciar la página. Para cumplir con la restricción de entregar un único archivo .html sin dependencias, usé el AudioContext nativo para generar un tono de 800Hz.

## Qué salió mal y cómo lo corregí

**Idioma inicial**. La primera iteración devolvió la estructura correcta pero con el idioma inicial correcto, arruinando la gracia del ejercicio de buscar frustrar al usuario. Lo corregí con otro prompt indicando que se fuerce el idioma inicial a chino.

**Layout Grid.** Inicialmente armé los botones de idioma con un display: grid. Aunque los márgenes y paddings estaban en 0, el grid forzaba matemáticamente una separación en celdas, impidiendo el efecto de "oración continua". Lo corregí reemplazando la primitiva de layout por Flexbox.

**Fallo en cascada por desincronización DOM/Estado.** Al agregar el control de volumen, el agente metió la lógica en JavaScript (getElementById, addEventListener) antes de inyectar correctamente el article en el HTML. Esto causó un TypeError: Cannot read properties of null, lo que detuvo la ejecución de todo el script e hizo que el audio dejara de funcionar. Lo corregí pasando el error al agente, el cual logró detectar que debia asegurarse de que la estructura semántica existiera en el DOM antes de intentar mutarla, y envolviendo la palabra "volumen" en un <span> para no pisar los botones al actualizar el texto.

## Prompts

El registro completo está en [prompts.md](prompts.md).
