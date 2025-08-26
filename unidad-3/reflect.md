# Unidad 3


## 🤔 Fase: Reflect

###📥 Actividad 12:

**Ecuación vectorial de la segunda ley de Newton**

F es la fuerza neta que actúa sobre un objeto, M es la masa del objeto, y A a es la aceleración resultante. La aceleración se obtiene sumando todas las fuerzas (como vectores) y dividiéndolas por la masa.

**¿Por qué es necesario multiplicar la aceleración por cero en cada frame del método update()?**

Porque la aceleración es acumulativa dentro de un frame, pero solo representa el efecto de las fuerzas en ese instante. Si no la reseteamos a cero, se seguirían acumulando fuerzas viejas en los frames siguientes, lo que sería físicamente incorrecto.

**Diferencia entre paso por valor y paso por referencia al aplicar fuerzas**

- **Paso por valor:** se crea una copia, y si la modificas no afecta el original.
- **Paso por referencia:** trabajas directamente sobre el mismo objeto en memoria, así que los cambios se reflejan en el original.
  
En p5.js, cuando paso un vector y lo modifico, normalmente es por referencia (ej. applyForce() suma a la aceleración del objeto real).

**Diferencia conceptual entre modelar fuerzas y definir algoritmos de aceleración**

Modelar fuerzas significa usar leyes físicas (fricción, gravedad, resortes) que tienen una base conceptual, se traducen en vectores de fuerza y se integran con la ecuación de Newton.

Definir algoritmos de aceleración es más libre: invento directamente cómo cambia la aceleración (ejemplo: asignarle un ruido Perlin o un valor aleatorio). No hay un modelo físico detrás, solo un comportamiento diseñado.

**Parte 2: Reflexión sobre mi proceso (Metacognición)**

**1. Lo más desafiante en la Actividad 10 (n-cuerpos)**

Definitivamente fue la implementación de las fuerzas mutuas. Entender que cada cuerpo atrae a todos los demás (y no a sí mismo) y que la simulación se vuelve de complejidad cuadrática me tomó un rato. La creatividad vino después, pero lo duro fue lograr que no se descontrolara.

**2. Las fuerzas produjeron lo que esperaba?**

Sí y no. Esperaba órbitas más limpias, pero me encontré con trayectorias caóticas, y eso me sorprendió. Lo interesante fue que esa “imperfección” terminó dando una estética mucho más viva y orgánica

**3. ¿Cómo cambió mi forma de pensar sobre la física en el arte generativo?**

Antes veía la física como algo rígido, ahora la veo como un motor creativo. Al modelar fuerzas descubrí que incluso pequeños ajustes (como cambiar G o añadir un resorte) cambian completamente la estética. La física se volvió un lenguaje visual más que solo un cálculo.

**4. Si tuviera una semana más**

Me gustaría explorar fuerzas electromagnéticas (atracción/repulsión alternando con el tiempo), o tal vez campos vectoriales dinámicos donde los cuerpos sigan flujos invisibles como corrientes de aire. También mejoraría la simulación optimizando las interacciones con estructuras como quadtrees, para poder tener cientos o miles de cuerpos.
