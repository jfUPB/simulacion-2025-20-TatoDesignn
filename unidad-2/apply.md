# Unidad 2


## 🛠 Fase: Apply

### 📥 Actividad 8:

**Concepto de la obra:** 

“Resorte Vivo”: una partícula se mueve como si estuviera unida por un resorte con amortiguación, hacia un objetivo que cambia en tiempo real. Ese objetivo se interpola entre tu mouse y un punto errante controlado por ruido Perlin, así la pieza alterna entre intención (seguir al usuario) y organicidad (vagar).

Visualmente, deja estelas suaves y colorea según la rapidez del movimiento; cuando el resorte “tira” fuerte, el trazo se intensifica.

**¿Cómo aplico MOTION 101 y por qué?**

Uso el pipeline clásico en cada frame:
1. position.add(velocity)
2. velocity.add(acceleration)
3. Limito magnitud y reinicio la aceleración tras integrar fuerzas.

Este marco me da una base clara para sumar fuerzas (resorte + amortiguación + opcionales) sin tocar directamente la posición; el carácter del movimiento emerge de la aceleración.

**Algoritmo de aceleración:**

Aceleración por resorte amortiguado (Hooke + damping) hacia un objetivo target:
	•	Hooke: F_spring = -k * (position - target)
	•	Amortiguación: F_damp = -c * velocity
	•	Fuerza total: F = F_spring + F_damp → acceleration = F / mass

Es distinto a “constante”, “aleatoria” o “hacia el mouse” puro: aquí la aceleración depende de la distancia y la velocidad actuales, generando oscilaciones controladas (segundo orden).

Interactividad:
	•	Mouse: define uno de los polos del objetivo.
	•	Ruido Perlin: el otro polo “vaga” solo.
	•	Teclas:
	•	SPACE: mezcla entre mouse (1) y Perlin (0) (toggle o animación).
	•	↑/↓: ajustan k (rigidez).
	•	←/→: ajustan c (amortiguación).
	•	M: alterna modo multi-partícula.

 **Codigo:**

 ```javascript
// Actividad 08 - "Resorte Vivo"
// Motion 101 + Vectores + Aceleración por resorte amortiguado (Hooke + damping)

let movers = [];
let useMulti = false;
let k = 0.06;      // rigidez del resorte
let c = 0.12;      // amortiguación
let mass = 1;
let mixMouse = 1;  // 1 = seguir mouse, 0 = seguir punto Perlin
let tx = 10_000, ty = 20_000; // offsets Perlin

function setup() {
  createCanvas(960, 540);
  initScene();
  background(10);
}

function initScene() {
  movers = [];
  let n = useMulti ? 60 : 1;
  for (let i = 0; i < n; i++) {
    movers.push(new Mover(createVector(random(width), random(height))));
  }
}

function draw() {
  // velo de desvanecimiento
  noStroke();
  fill(10, 10, 15, 18);
  rect(0, 0, width, height);

  // objetivo: mezcla entre mouse y perlin wander
  let wander = createVector(
    map(noise(tx), 0, 1, 0, width),
    map(noise(ty), 0, 1, 0, height)
  );
  tx += 0.0045;
  ty += 0.0038;

  let mouseV = createVector(mouseX, mouseY);
  let target = p5.Vector.lerp(wander, mouseV, constrain(mixMouse, 0, 1));

  // dibuja objetivo
  noFill();
  stroke(120, 180, 255, 140);
  circle(target.x, target.y, 18);

  // actualiza movers
  for (let m of movers) {
    // Fuerza de Hooke + damping
    let toTarget = p5.Vector.sub(m.position, target);   // desplazamiento
    let Fspring = toTarget.mult(-k);                    // -k * x
    let Fdamp = p5.Vector.mult(m.velocity, -c);         // -c * v
    let F = p5.Vector.add(Fspring, Fdamp);

    m.applyForce(F);       // a = F/m
    m.update();            // Motion 101 integration
    m.edgesWrap();         // envolver bordes
    m.show();              // dibujar
  }

  drawHUD(target);
}

class Mover {
  constructor(pos) {
    this.position = pos.copy();
    this.velocity = p5.Vector.random2D().mult(random(1, 2));
    this.acceleration = createVector(0, 0);
    this.maxSpeed = 12;
    this.maxAccel = 1.2;
  }

  applyForce(f) {
    // a = F / m  (mass = 1)
    this.acceleration.add(p5.Vector.limit(f, this.maxAccel));
  }

  update() {
    this.velocity.add(this.acceleration);
    this.velocity.limit(this.maxSpeed);
    this.position.add(this.velocity);
    this.acceleration.mult(0); // reset
  }

  show() {
    let speed = this.velocity.mag();
    let hue = map(speed, 0, this.maxSpeed, 180, 340); // color por velocidad
    strokeWeight(2);
    stroke(color(`hsla(${hue}, 90%, 60%, 0.9)`));
    point(this.position.x, this.position.y);

    // trazo direccional
    push();
    translate(this.position.x, this.position.y);
    rotate(this.velocity.heading());
    strokeWeight(1.8);
    line(0, 0, 14 + speed, 0);
    pop();
  }

  edgesWrap() {
    if (this.position.x > width)  this.position.x = 0;
    if (this.position.x < 0)      this.position.x = width;
    if (this.position.y > height) this.position.y = 0;
    if (this.position.y < 0)      this.position.y = height;
  }
}

function keyPressed() {
  if (key === ' ') {           // alterna mezcla: mouse vs perlin
    mixMouse = mixMouse > 0.5 ? 0 : 1;
  }
  if (keyCode === UP_ARROW)   k = min(k + 0.01, 0.5);
  if (keyCode === DOWN_ARROW) k = max(k - 0.01, 0.0);
  if (keyCode === RIGHT_ARROW) c = min(c + 0.01, 0.8);
  if (keyCode === LEFT_ARROW)  c = max(c - 0.01, 0.0);

  if (key === 'M' || key === 'm') {
    useMulti = !useMulti;
    initScene();
  }
}

function drawHUD(target) {
  noStroke();
  fill(255);
  textSize(12);
  text(
    `k (rigidez): ${k.toFixed(2)}   c (amortiguación): ${c.toFixed(2)}   ` +
    `mixMouse (SPACE): ${mixMouse.toFixed(1)}   movers (M): ${useMulti ? 'muchos' : 'uno'}`,
    14, height - 14
  );
  // pequeña cruz en el target
  stroke(255, 220);
  line(target.x - 5, target.y, target.x + 5, target.y);
  line(target.x, target.y - 5, target.x, target.y + 5);
}
```
**Enlace**
https://editor.p5js.org/TatoDesignn/sketches/TyW9fIFmy

<img width="843" height="593" alt="Captura de pantalla 2025-08-11 a la(s) 4 47 02 p m" src="https://github.com/user-attachments/assets/2a979e4b-f055-4ca3-875d-f9017af5c173" />




