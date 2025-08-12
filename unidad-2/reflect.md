# Unidad 2


## 🤔 Fase: Reflect

### 📥Actividad 9:

**Receta del marco MOTION 101:**

1.	Calcular o tener la aceleración.
2.	Sumar la aceleración a la velocidad.
3.	Sumar la velocidad a la posición.
4.	Dibujar el objeto en la nueva posición.

**Relación de MOTION 101 con position, velocity y acceleration:**

- position indica dónde está el objeto.
- velocity define cuánto y en qué dirección se moverá cada frame.
- acceleration cambia la velocidad a lo largo del tiempo.
- En Motion 101, todo se transmite en cadena: aceleración → velocidad → posición.

**Explicación geométrica de Motion 101:**

Lo imagino como flechas que se suman:
- Una flecha grande (posición) desde el origen hasta el objeto.
- Una flecha pequeña (velocidad) que se pega a la punta de la primera para moverla.
- Otra flecha aún más pequeña (aceleración) que ajusta la velocidad con el tiempo.
- Cada paso es simplemente sumar vectores, lo que se traduce en un movimiento suave y progresivo.

**Lo más desafiante en la Actividad 08:**

Definir bien el algoritmo de aceleración. La idea del resorte amortiguado no era difícil de entender, pero sí lo fue ajustar sus parámetros (k y c) para que se sintiera natural y estable. La interactividad fue más fácil de integrar porque ya tenía claro qué quería controlar con el mouse y las teclas.

**¿El algoritmo produjo el efecto esperado? Momento sorpresa:**

Sí, logré ese “efecto elástico” que buscaba. El momento sorpresa fue cuando ajusté la amortiguación y el movimiento empezó a oscilar como si realmente tuviera inercia física. Al principio oscilaba demasiado y parecía un insecto frenético, pero bajando k y subiendo c logré un comportamiento más realista.

**Cómo ha cambiado mi forma de pensar sobre el movimiento:**

Antes veía el movimiento en pantalla como un simple cambio de coordenadas. Ahora lo entiendo como un sistema de relaciones entre fuerzas, aceleraciones, velocidades y posiciones. Es mucho más natural y flexible pensar en términos de vectores y dejar que la física haga el resto.

**Si tuviera una semana más:**

Me gustaría probar un algoritmo de aceleración basado en campos de flujo (flow fields) para que el movimiento siga patrones invisibles en el espacio. También me gustaría experimentar con atractores y repulsores múltiples para lograr comportamientos más complejos e impredecibles.

### 📥 Actividad 10:

**Lo que funciona:**

- La idea de trails multicolor que “pintan” el lienzo está clara y sí se ve reflejada en el sketch.
- El círculo central como ancla visual ayuda a entender de dónde emergen los trazos.

**Qué mejoraría:**

- Un par de líneas en la bitácora sobre qué experiencia busca el espectador (calma orgánica vs. caos energético) alinea mejor diseño y parámetros.

**Bien aplicado:**

Flujo correcto: acceleration → velocity → position + acceleration.mult(0) al final.

**Ajustes pro:**

Tu Lévy puede producir saltos enormes (por la potencia negativa) y a veces “sucia” la composición. 

**Punto fuerte:**

Elegir Lévy flight para “trazos llamativos” es buena intuición: mezcla paseos cortos con saltos largos y genera texturas interesantes.

**Código / legibilidad**

Clase clara y responsabilidades separadas.

### 📥Actividad 11:

**Continuar:**

Lo más útil fue trabajar con el marco Motion 101 integrado a diferentes algoritmos de aceleración. Me ayudó a entender que, controlando bien la aceleración, se pueden generar movimientos muy distintos sin necesidad de modificar la posición directamente.

**Dejar de hacer:**

Algunas actividades muy cortas de introducción (tipo solo modificar un valor y observar) se sintieron un poco repetitivas. Podrían compactarse para dejar más tiempo a los experimentos completos o a integrar varios conceptos en una sola pieza.

**Empezar a hacer:**

Me gustaría explorar más fuerzas físicas como fricción, atracción, repulsión y resortes, pero aplicadas en sistemas de partículas

**Método de aprendizaje:**

El paso de Seek (experimentos guiados) a Apply (obra libre) me pareció natural y efectivo porque primero entendí las piezas individuales y luego las integré a mi manera. La única mejora sería añadir un “mini desafío” de integración antes de la obra final.
