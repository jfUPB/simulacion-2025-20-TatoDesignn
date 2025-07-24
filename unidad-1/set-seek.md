# Unidad 1

## 🔎 Fase: Set + Seek

### 📥Actividad 1:
**¿Cómo la aleatoriedad influye en el arte generativo?**

Para mí, la aleatoriedad en el arte es clave porque rompe la monotonía y permite que colores, formas y movimientos cambien constantemente, haciendo que todo sea más interesante.

### 📥Actividad 2:
**¿Cuál es el papel de la aleatoriedad en su obra?**

Toda la obra funciona de manera aleatoria, si nos fijamos cada reproducción es totalmente diferente a la anterior, sus formas de raíz se transforman de manera diferente, pero siempre con la sincronización de la canción. Las formas y colores son las variantes que tenemos en la obra y son dependientes de la aleatoriedad del programa.

**¿Cómo se aplica el concepto de aleatoriedad en el tipo de proyectos que desarrollas?**

En mi caso tengo varios ejemplos donde la aleatoriedad toma una papel muy importante en el proyecto, desde el mas sencillo hasta algunos mas complejos:

1. Proyecto de practica en python: En este proyecto el programa escoge un numero aleatorio para que el usuario trate de adivinarlo, esto por medio de la librería *Random*

2. Juego de cartas en Unity: En este proyecto los usuarios roban cartas de una baraja, para que la baraja no siempre fuera igual hacemos uso de lo aleatorio, declaramos una variable y la inicializamos como un numero aleatorio y luego este numero lo utilizamos para dar una posición en una lista que contiene todas las cartas 

3. Programación de boss final: Para que estas batallas contra un jefe final sean mas emocionantes hacemos uso de los ataques aleatorios

### 📥Actividad 3:
**¿Que espero que suceda?**
Quiero hacer que mi punto a medida que se desplaza de manera aleatoria en mi canvas, deje a su paso una especie de estela de su recorrido aleatorio con colores aleatorios.

**¿Que sucedio?**
El punto empezo a desplazarse de manera aleatoria en mi Canvas y a medida que se movía deja una marca de su recorrido anterior con colores random.

**¿Ocurrio lo que esperaba**
Si!, Ocurrio exactamente lo que trate de realizar, para los parametros que recibe *stroke()* simplemente le pase la funcion *random()* con el parametro hasta 255

### 📥Actividad 4:
**¿cuál es la diferencia entre una distribución uniforme y una no uniforme de números aleatorio?**

**Distribución Uniforme:** En este utilizamos nuestra función *random* para generar números aleatorios en el rango dado en el parámetro, esto genera puntos aleatorios de manera uniforme ya que todos los números tienen la misma probabilidad de ocurrir.

**Distribución no uniforme:** Para este utilizamos la función *randomGaussian*, con la cual tendremos un valor **medio** que nos servirá para generar valores alrededor de este, favoreciendo los valores mas cercanos a la media. 

**Modifica el código de la caminata aleatoria para que utilice una distribución no uniforme, favoreciendo el movimiento hacia la derecha.**
[Codigo modificado](https://editor.p5js.org/TatoDesignn/sketches/JeDPP52k3)

### 📥Actividad 5:

**Codigo:**
```javascript
let walker;

function setup() {
  createCanvas(640, 240);
  walker = new Walker();
  background(255);
}

function draw() {
  walker.step();
  walker.show();
}

class Walker {
  constructor() {
    this.x = width / 2;
    this.y = height / 2;
  }

  show() {
    stroke(random(255), random(255), random(255));
    point(this.x, this.y);
  }

  step() {
    let stepX = randomGaussian(1, 2); // media = 1, sd = 2
    this.x += stepX;

    let stepY = randomGaussian(0, 1); 
    this.y += stepY;
  }
}
``` 
**Enlace al programa**
[Programa](https://editor.p5js.org/TatoDesignn/full/abeXsyD9Q)

### 📥Actividad 6:

**Explica por qué usaste esta técnica y qué resultados esberabas obtener**

La distribución personalizada o de rechazo y aceptación nos permite generar pasos de manera aleatoria pero mas grandes evitando pasos pequeños y repetitivos en el mismo lugar 

**Codigo:**
```javascript
let walker;

function setup() {
  createCanvas(640, 240);
  walker = new Walker();
  background(255);
}

function draw() {
  walker.step();
  walker.show();
}

class Walker {
  constructor() {
    this.x = width / 2;
    this.y = height / 2;
  }

  show() {
    stroke(0, 50);
    point(this.x, this.y);
  }

  step() {

    let maxStep = 20;

    let stepx = randomSignedStep(maxStep);
    let stepy = randomSignedStep(maxStep);

    this.x += stepx;
    this.y += stepy;
  }
}


function randomSignedStep(max) {
  while (true) {
    let r1 = random(1);
    let r2 = random(1);
    let probability = r1 * r1; 

    if (r2 < probability) {
      let step = r1 * max; 
      return random() < 0.5 ? -step : step; 
    }
  }
}
```
**Enlace al programa:**
[Programa](https://editor.p5js.org/TatoDesignn/full/IAKQfTFdX)

### 📥Actividad 7:

**Qué resultados esperaba obtener**

Quiero controlar el tamaño del circulo, tambien cambiar color con el que pinta y aplicarle un parámetro alpha para generar ese desvanecimiento anterior visto en los ejercicios.

**Codigo:**
```javascript
// The Nature of Code
// Daniel Shiffman
// http://natureofcode.com

let walker;

function setup() {
  createCanvas(640, 240); 
  walker = new Walker(); 
  background(255);
}

function draw() {
  fill(255, 10);
  noStroke();
  rect(0, 0, width, height);

  walker.step();
  walker.show();
}

class Walker {
  constructor() {
    this.tx = 0;
    this.ty = 10000;
    this.ts = 20000; 
    this.tc = 30000; 
  }

  step() {
    this.x = map(noise(this.tx), 0, 1, 0, width);
    this.y = map(noise(this.ty), 0, 1, 0, height);
    this.size = map(noise(this.ts), 0, 1, 10, 60); 
    this.color = map(noise(this.tc), 0, 1, 50, 200); 

    this.tx += 0.01;
    this.ty += 0.01;
    this.ts += 0.01;
    this.tc += 0.01;
  }

  show() {
    noStroke();
    fill(this.color, 100, 180, 180); 
    ellipse(this.x, this.y, this.size, this.size);
  }
}
```
**Enlace del programa:**
[Programa](https://editor.p5js.org/TatoDesignn/sketches/1tokCmTAM)

