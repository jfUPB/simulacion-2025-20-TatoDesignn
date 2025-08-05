# Unidad 1

## 🛠 Fase: Apply

### 📥Actividad 8:

**1. Un texto donde expliques el concepto de obra generativa.**

Para esta obra, mi intención fue simular un entorno orgánico en constante movimiento, donde pequeños entes circulares flotan y dejan un rastro efímero al moverse. Estos entes no solo se desplazan con ruido Perlin (lo que les da una fluidez natural), sino que también reaccionan al movimiento del mouse cambiando de dirección, tamaño y color, haciendo de la obra un escenario aleatorio.

Cuando presionas y mantienes el clic del mouse, activas la parte interactiva de la obra:

- El círculo cambia de dirección de forma más dinámica.
- Su tamaño varía aleatoriamente con mayor intensidad.
- El color se transforma con una paleta controlada por una distribución gaussiana, generando combinaciones suaves y estéticas.

**2. Codigo:**
```javascript
let walker;

function setup() {
  createCanvas(1280, 720);
  walker = new Walker();
  background(0);
}

function draw() {
  fill(0, 10);
  rect(0, 0, width, height); // Desvanecimiento del rastro

  walker.update();
  walker.display();
}

class Walker {
  constructor() {
    this.tx = random(1000);
    this.ty = random(1000);
    this.ts = random(1000);
    this.tc = random(1000);
    this.size = 20;
    this.col = color(255, 150);
  }

  update() {
    this.x = map(noise(this.tx), 0, 1, 0, width);
    this.y = map(noise(this.ty), 0, 1, 0, height);
    this.size = map(noise(this.ts), 0, 1, 10, 60);

    // Modificación con interacción del mouse
    if (mouseIsPressed) {
      this.tx += random(0.01, 0.05);
      this.ty += random(0.01, 0.05);
      this.ts += random(0.01, 0.05);

      // Color con distribución no uniforme (Gaussian)
      let r = constrain(randomGaussian(180, 40), 0, 255);
      let g = constrain(randomGaussian(100, 60), 0, 255);
      let b = constrain(randomGaussian(220, 30), 0, 255);
      this.col = color(r, g, b, 180);
    } else {
      this.tx += 0.005;
      this.ty += 0.005;
      this.ts += 0.005;
    }
  }

  display() {
    noStroke();
    fill(this.col);
    ellipse(this.x, this.y, this.size);
  }
}
```

**3. Enlace al programa:**
[Programa](https://editor.p5js.org/TatoDesignn/sketches/B9CkAfNEZ)

**4. Captura de pantalla:**

<img width="706" height="652" alt="image" src="https://github.com/user-attachments/assets/76e4f90d-07c8-44e5-8c29-2b0af4cf0f9e" />

