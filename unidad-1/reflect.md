# Unidad 1

## 🤔 Fase: Reflect

### 📥 Actividad 9:

**Parte 1: Recuperación de conocimiento**

**¿Cuál es la diferencia entre random() y noise() y cuándo usarías cada una?**

*random()* genera números totalmente impredecibles, cada valor que da no tiene conexión con el anterior. En cambio, *noise()* aunque parece aleatorio, da valores suaves, conectados entre sí, como si fueran movimientos naturales. Usaría *random()* para cosas que deben ser completamente impredecibles y *noise()* cuando quiero un movimiento más fluido y natural

**¿Qué es una distribución de probabilidad y cómo se ven las diferencias entre una caminata con uniforme vs normal?**

Es como una regla que define qué tan probable es que salga cierto valor. En una distribución uniforme, todos los valores tienen la misma posibilidad de salir, así que en una caminata, el punto se mueve muy parejo en todas direcciones. En cambio, en una distribución normal (o gaussiana), la mayoría de los pasos son pequeñitos y los extremos casi no ocurren, así que la caminata se concentra más al centro, con movimientos más suaves y agrupados.

**¿Cuál es el papel de la aleatoriedad en el arte generativo?**

La aleatoriedad es súper importante porque le da vida y variación a la obra. Por un lado, permite que cada ejecución sea única, y por otro lado, da ese toque de lo impredecible que hace que las formas, colores o movimientos no se vean repetitivos o aburridos.

**En tu obra final, ¿qué concepto usaste y por qué fue una buena elección?**

Usé noise() para mover al walker y que pareciera que flotaba o se desplazaba de manera natural. Esto me ayudó a darle esa sensación orgánica que quería lograr desde el principio, como si tuviera su propia lógica de movimiento. También mezclé randomGaussian() para los colores, y eso hizo que los cambios fueran suaves pero con carácter.

**¿Qué es una caminata y qué tiene de especial la caminata tipo Lévy flight?**

Una caminata es básicamente moverse paso a paso, pero de forma aleatoria, como un personaje que no sabe bien a dónde va. En el caso del Lévy flight, lo particular es que en vez de moverse de a pasitos iguales, a veces da saltos larguísimos.

**Parte 2: Reflexión sobre el proceso**

**¿Cuál fue el concepto más difícil para ti? ¿Cómo lo entendiste al final?**

Creo que lo que más me costó al principio fue visualizar las distribuciones, sobre todo la gaussiana. No entendía bien cómo se aplicaba en código o cómo iba a afectar visualmente. Lo resolví probando mucho en p5.js y viendo cómo se formaban grupos de puntos o cómo afectaba el color. Hacerlo visible me ayudó a entenderlo de verdad.

**¿Algún error que terminó siendo una buena idea?**

Sí, hubo un momento en que aumenté mucho la opacidad del fondo y parecía que el rastro del walker desaparecía, pero luego lo bajé a un valor muy bajo y ese efecto de “difuminado” quedó buenísimo.

**¿Cuál fue el mayor desafío al combinar técnicas de aleatoriedad?**
Lo más difícil fue lograr que no se pisaran entre sí, como que noise() no opacara el efecto del randomGaussian(). Tuve que ajustar bastante los rangos y velocidades para que todo se sintiera armónico, especialmente cuando el usuario interactuaba.

**¿Qué harías diferente si empezaras otra vez?**
Probaría desde el principio con múltiples walkers o elementos interactuando entre sí. También planearía mejor la parte de la interacción, porque fue algo que agregué después y creo que podría haber sido más protagónica si lo pensaba desde el arranque.


### 📥 Actividad 10:

** Retroalimentación a la Actividad 08 de Juan jose Amaya**

Primero que todo, felicitar al compañero. Me pareció muy interesante el enfoque de representar un paisaje natural usando elementos de aleatoriedad. Me gustó mucho cómo dividiste la obra en capas con funciones distintas: el cielo, los árboles y el río. Eso le da estructura visual y hace que cada componente tenga su propia lógica.

La obra logra un muy buen balance entre lo técnico y lo visual. Me gustó que lo pensaras como un “paisaje vivo” más que como una animación abstracta sin sentido.

### 📥 Actividad 11:

**Continuar:**

La actividad que más me ayudó a entender todo fue la de modificar el código del walker usando diferentes distribuciones. Poder ver la diferencia entre random(), randomGaussian() y noise() en vivo me hizo comprender realmente cómo afectan el movimiento y el comportamiento de un objeto en pantalla.

**Dejar de hacer:**

Hubo momentos en los que algunas explicaciones sentí que se repetían un poco o no agregaban algo nuevo, especialmente en los primeros textos sobre aleatoriedad.

**Empezar a hacer:**

Me faltaron ejemplos de obras reales de artistas que usen estas técnicas. Sería increíble ver referencias visuales o videos de obras generativas de artistas actuales que trabajen con código.

**Balance Teoría–Práctica:**

Creo que estuvo bastante bien equilibrado. Me gustó que primero analizáramos conceptos y después pasáramos directo al código. Sí sentí que la mejor parte fue cuando tocaba programar directamente, así que quizás se podrían reducir un poco más los textos o hacerlos más visuales para que no se sientan pesados

**Comentario adicional:**

En general, disfruté bastante la unidad. Me gustó que estuviera pensada para ir paso a paso, que tuviera libertad creativa, y que al final pudiera hacer una obra mía aplicando todo.
