# Unidad 2

## 🔎 Fase: Set + Seek


### 📥 Actividad 1:

**¿Cómo funciona la suma de dos vectores en p5.js?**

En p5.js, cuando usamos vectores, la suma de dos vectores se hace usando el método *.add()*. Por ejemplo, al hacer position.add(velocity); estámos actualizando la posición sumándole directamente las componentes *x* y *y* del vector de velocidad. Es decir, el objeto position cambia su valor interno con esta operación.

**¿Por qué esta línea position = position + velocity; no funciona?**

Esa línea no funciona porque en p5 no se puede sumar objetos p5.Vector como si fueran números normales. Cuando hacemos position + velocity, se está intentando sumar dos objetos directamente, lo cual no es válido en JavaScript. Para esto es que nos sirve la funcion *.add()*

### 📥 Actividad 2:

**¿Qué tuve que hacer para hacer la conversión?**

Lo que hice fue tomar el clásico walker que se movía sumando directamente x y y con valores aleatorios, y lo transformé para que en lugar de trabajar con variables sueltas, todo se manejara con un p5.Vector.

Eso me permitió agrupar la posición en un solo objeto (position) y usar métodos como .add() para mover al walker. Además, la lógica del movimiento se volvió más limpia y fácil de entender

**Codigo:**

```javascript
let walker;

function setup() {
  createCanvas(640, 360);
  walker = new Walker();
  background(255);
}

function draw() {
  walker.step();
  walker.display();
}

class Walker {
  constructor() {
    this.position = createVector(width / 2, height / 2);
  }

  step() {
    let step = createVector(random(-1, 1), random(-1, 1));
    this.position.add(step);
  }

  display() {
    stroke(0);
    point(this.position.x, this.position.y);
  }
}
```
### 📥Actividad 3:

**¿Qué resultado esperaba obtener?**

Antes de correrlo, pensé que se iba a imprimir primero el vector original (6, 9) y luego, que como se usaba una función aparte para modificarlo, no iba a cambiar, porque pensé que al pasarlo como argumento sería por valor, como con los números o strings.

**¿Qué resultado obtuve?**

```javascript
p5.Vector Object : (6, 9)
p5.Vector Object : (20, 30)
Only once
```

Entonces sí cambió el valor del vector original dentro de la función playingVector(), lo cual me confirmó que los vectores se pasan por referencia.

**Paso por valor vs paso por referencia en JavaScript**

Paso por valor ocurre con tipos primitivos como number, string, boolean, etc.

```javascript
let a = 5;
function cambiar(x) {
  x = 10;
}
cambiar(a);
console.log(a); // 5, no cambia
```

Paso por referencia ocurre con objetos (como p5.Vector, arrays, objetos literales):

```javascript
let obj = { x: 5 };
function modificar(o) {
  o.x = 10;
}
modificar(obj);
console.log(obj.x); // 10, sí cambia
```

**¿Qué tipo de paso se está haciendo en el código?**

Se está haciendo paso por referencia, porque al pasar el vector a la función playingVector(), cualquier cambio que se haga dentro de esa función afecta directamente al objeto original

**¿Qué aprendí?**

Aprendí que los objetos como los vectores en p5.js no se copian cuando se pasan a una función, sino que se comparten. Eso significa que cualquier modificación interna cambia el objeto original.

### 📥Actividad 4:

**¿Para qué sirve el método mag()?**

El método .mag() sirve para obtener la magnitud o longitud del vector, es decir, qué tan largo es. Si lo vemos como una flecha desde el punto (0,0) hasta su posición, mag() nos da la distancia total de esa flecha.

**¿Y magSq()? ¿Cuál es la diferencia?**

El método .magSq() hace lo mismo, pero devuelve el valor al cuadrado de la magnitud. O sea, no calcula la raíz cuadrada como mag().

**¿Para qué sirve el método normalize()?**

normalize() sirve para convertir un vector a una versión de sí mismo con magnitud 1, manteniendo la dirección. Es decir, apunta hacia el mismo lugar, pero se convierte en un vector unitario.

**¿Qué le diría al periodista si me pregunta: “¿para qué sirve dot()?”?**

El método dot() sirve para saber cuánto dos vectores apuntan en la misma dirección. Es como medir qué tan alineados están.

**¿Cuál es la diferencia entre dot() como método de instancia y como método estático?**

**Instancia:** v1.dot(v2) se usa cuando ya tienes un vector y quieres hacer el producto punto con otro.

**Estática:** p5.Vector.dot(v1, v2) se usa directamente sin tener que invocar el método desde un objeto. Ambas hacen lo mismo, pero es cuestión de estilo o contexto.

**¿Cuál es la interpretación geométrica del producto cruz (cross())?**

El producto cruz da como resultado un nuevo vector perpendicular al plano formado por los dos vectores originales.
Su dirección está determinada por la regla de la mano derecha (si apuntas los dedos de una mano en la dirección del primer vector y los cierras hacia el segundo, el pulgar apunta hacia el resultado).

**¿Para qué sirve el método dist()?**

dist() sirve para medir la distancia entre dos puntos en el espacio, cuando esos puntos están representados como vectores. Por ejemplo, si tienes dos bolas moviéndose en pantalla, puedes calcular con dist() cuán lejos están entre sí.

**¿Para qué sirven normalize() y limit()?**

*normalize()* te da un vector con la misma dirección pero magnitud 1, útil para tener solo la dirección.

*limit()* te permite restringir la magnitud máxima de un vector, muy útil cuando trabajas con velocidades o fuerzas y no quieres que se descontrole. Por ejemplo: limitar la velocidad máxima de un objeto.

### 📥Actividad 5:

**Codigo:**

```javascript
let t = 0;
let speed = 0.01;

function setup() {
  createCanvas(400, 400);
}

function draw() {
  background(180);

  let origen = createVector(100, 300);     
  let azul = createVector(0, -200);         
  let rojo = createVector(200, 0);          
  let verde = p5.Vector.add(azul, rojo);    

  let esquinaAzul = p5.Vector.add(origen, azul);             
  let esquinaRoja = p5.Vector.add(esquinaAzul, rojo);        
  let puntoVerde = p5.Vector.lerp(origen, esquinaRoja, t); 

  drawArrow(origen, azul, 'blue');
  drawArrow(esquinaAzul, rojo, 'red');
  drawArrow(origen, verde, 'green');

  let vectorMorado = p5.Vector.sub(puntoVerde, esquinaAzul);
  drawArrow(esquinaAzul, vectorMorado, 'purple');

  t += speed;
  if (t > 1 || t < 0) {
    speed *= -1;
  }
}

function drawArrow(base, vec, myColor) {
  push();
  stroke(myColor);
  strokeWeight(3);
  fill(myColor);
  translate(base.x, base.y);
  line(0, 0, vec.x, vec.y);
  rotate(vec.heading());
  let arrowSize = 10;
  translate(vec.mag() - arrowSize, 0);
  triangle(0, arrowSize / 2, 0, -arrowSize / 2, arrowSize, 0);
  pop();
}
```
**¿Cómo funciona lerp() y lerpColor()?**

Lerp() es una función, que nos permite encontrar un punto entre dos valores (o vectores) dependiendo de un parámetro t que va de 0 a 1.

**¿Cómo se dibuja una flecha usando drawArrow()?**

La función drawArrow(base, vec, color) que usamos dibuja una flecha en pantalla a partir de:
- Un punto de inicio (base)
- Un vector de dirección y magnitud (vec)
- Un color

### 📥 Actividad 6:

**¿Cuál es el concepto del marco Motion 101 y cómo se interpreta geométricamente?**

Motion 101 es el marco más básico de movimiento en programación con vectores. Funciona de la siguiente manera:

1. Un objeto tiene una posición (dónde está).
2. Tiene una velocidad (hacia dónde se mueve y qué tan rápido).
3. En cada cuadro (draw()), la velocidad se suma a la posición.

Geométricamente, lo podemos imaginar como una flecha (velocidad) que se añade a otra flecha (posición), desplazando el objeto en esa dirección.

**¿Cómo se aplica Motion 101 en el ejemplo 1.7?**

En el ejemplo 1.7, el autor crea una clase Mover que encapsula toda la lógica del movimiento.

En el constructor, el objeto tiene dos vectores:
- position: lugar inicial aleatorio.
- velocity: dirección y velocidad aleatoria.

### 📥 Actividad 7:

**Experimento: probé tres tipos de aceleración**

**Aceleración constante:**
- Le di al objeto una aceleración fija a (0.05, 0.01).
- El objeto empezó lento, pero fue aumentando su velocidad de forma constante. El movimiento se volvió más y más rápido hasta que salió de pantalla (si no había límite).
- Observación: Se siente como un cohete despegando: acelera de forma progresiva y no se detiene. Muy útil para simular gravedad o impulso.

**Aceleración aleatoria:**
- En cada cuadro le asigné un nuevo vector de aceleración aleatorio.
- El objeto se movía de forma errática, como si fuera una partícula en el aire. A veces aceleraba mucho, otras veces se frenaba de golpe.
- Ideal para simular comportamiento orgánico, partículas, o movimientos impredecibles. No hay control, pero sí una sensación de vida.

**Aceleración hacia el mouse:**

- Calcule un vector desde la posición del objeto hacia el puntero del mouse, lo normalizo y lo multiplico por una fuerza.
- El objeto perseguía al mouse suavemente. Mientras más lejos estaba, más rápido intentaba alcanzarlo. Si pasabas cerca, hacía curvas suaves para seguirte.
- Este fue el más interesante visualmente. El movimiento es natural, como si el objeto tuviera “intención” o “curiosidad”. Perfecto para comportamientos inteligentes o seguimiento.
