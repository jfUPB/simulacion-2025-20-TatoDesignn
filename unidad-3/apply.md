# Unidad 3


## 🛠 Fase: Apply

### 📥 Actividad 10:

Un conjunto de “placas” (cuerpos) cuelga de varios anclajes superiores como un móvil de Calder, simulando los radares o molinos de viento. 
Cada placa: se mueve con las fuerzas: gravedad mutua (n‑cuerpos), resorte del “brazo” que la sostiene y un viento suave de ruido Perlin;

Arrastrar y soltar: crea una nueva placa con velocidad inicial = vector de arrastre.
- B: añade una placa colgada de un anclaje.
- A: añade un anclaje nuevo arriba.
- G/H: disminuye/aumenta G.
- W: alterna viento Perlin.
- S: alterna resortes (modo móvil ON/OFF).
- ESPACIO: alterna estela.
- C: limpia estelas.
- R: reinicia.

```javascript
// "Mobile de N‑cuerpos" — n-body + Calder + Perlin wind + randomGaussian
// Interacción: drag-to-launch, B: body colgado, A: anclaje, G/H: G, W: viento,
// S: resortes on/off, SPACE: trail, C: limpiar, R: reset.

let bodies = [];
let anchors = [];
let rods = []; // resortes amortiguados anclaje->cuerpo

let G = 1.1;
let EPS = 120;              // suavizado gravitacional
let useWind = true;
let useSprings = true;
let trail = true;

let tWind = 0;
const WIND_AMP = 0.12;
const WIND_SCALE = 0.0025;

let dragStart = null;

function setup() {
  createCanvas(960, 540);
  background(8);
  initScene();
}

function initScene() {
  bodies = [];
  anchors = [];
  rods = [];
  // anclajes tipo Calder (3-4 puntos superiores que se mecen con ruido)
  let cols = 4;
  for (let i = 0; i < cols; i++) {
    let ax = map(i, 0, cols-1, width*0.15, width*0.85);
    anchors.push(new Anchor(ax, height*0.18, random(1e6)));
  }
  // generar algunas placas colgadas
  for (let i = 0; i < 8; i++) addHangingBody();
}

function draw() {
  if (trail) { noStroke(); fill(8, 16); rect(0,0,width,height); }
  else background(8);

  // actualiza anclajes (suave vaivén perlin)
  for (let a of anchors) a.update();

  // fuerzas n‑cuerpos (simétricas i<j)
  for (let i = 0; i < bodies.length; i++) {
    for (let j = i+1; j < bodies.length; j++) {
      let f = gravity(bodies[i], bodies[j]);
      bodies[i].applyForce(f);
      bodies[j].applyForce(p5.Vector.mult(f, -1)); // 3ª ley de Newton
    }
  }

  // resortes (varillas)
  if (useSprings) {
    for (let r of rods) r.apply();
  }

  // viento Perlin
  if (useWind) {
    tWind += 0.006;
    for (let b of bodies) {
      let ang = noise(b.pos.x*WIND_SCALE, b.pos.y*WIND_SCALE, tWind) * TAU;
      let wind = p5.Vector.fromAngle(ang).mult(WIND_AMP);
      b.applyForce(wind);
    }
  }

  // integrar y dibujar
  for (let b of bodies) {
    b.update();
    b.wrap();
    b.show();
  }

  // dibujar anclajes y varillas
  for (let r of rods) r.show();
  for (let a of anchors) a.show();

  // guía lanzamiento
  if (dragStart) {
    stroke(255, 200); strokeWeight(2);
    line(dragStart.x, dragStart.y, mouseX, mouseY);
    noStroke(); fill(255); circle(dragStart.x, dragStart.y, 4);
  }

  // HUD
  noStroke(); fill(255);
  text(`bodies: ${bodies.length} — anchors: ${anchors.length} — rods: ${rods.length} — G: ${G.toFixed(2)} (G/H) — W viento: ${useWind?'ON':'OFF'} — S resortes: ${useSprings?'ON':'OFF'} — drag to launch, B add hanging, A add anchor, SPACE trail, C clear, R reset`, 12, height-12);
}

// ---------- Física ----------
function gravity(b1, b2) {
  let dir = p5.Vector.sub(b2.pos, b1.pos);
  let distSq = dir.magSq() + EPS;
  dir.normalize();
  let strength = (G * b1.m * b2.m) / distSq;
  return dir.mult(strength); // fuerza sobre b1 hacia b2
}

// ---------- Clases ----------
class Body {
  constructor(x, y, m) {
    this.pos = createVector(x, y);
    this.vel = p5.Vector.random2D().mult(random(0.4, 1.2));
    this.acc = createVector(0, 0);
    this.m = m;                     // masa
    this.size = map(this.m, 0.4, 3.0, 4, 16, true);
    this.prev = this.pos.copy();
    // paleta suave por velocidad + leve variación gaussiana
    let baseH = 210 + randomGaussian(0, 12);
    this.col = color(`hsla(${baseH}, 70%, 65%, 0.95)`);
    this.maxSpeed = 26;
  }
  applyForce(f) { this.acc.add(p5.Vector.div(f, this.m)); }
  update() {
    this.vel.add(this.acc).limit(this.maxSpeed);
    this.prev.set(this.pos);
    this.pos.add(this.vel);
    this.acc.mult(0);
  }
  wrap() {
    if (this.pos.x > width)  { this.pos.x = 0;  this.prev.set(this.pos); }
    if (this.pos.x < 0)      { this.pos.x = width; this.prev.set(this.pos); }
    if (this.pos.y > height) { this.pos.y = 0;  this.prev.set(this.pos); }
    if (this.pos.y < 0)      { this.pos.y = height; this.prev.set(this.pos); }
  }
  show() {
    // punto + halo
    noStroke();
    fill(this.col);
    circle(this.pos.x, this.pos.y, this.size);
    // estela
    strokeWeight(1.2);
    stroke(red(this.col), green(this.col), blue(this.col), 140);
    line(this.prev.x, this.prev.y, this.pos.x, this.pos.y);
  }
}

class Anchor {
  constructor(x, y, seed) {
    this.base = createVector(x, y);
    this.pos  = this.base.copy();
    this.nx = seed; this.ny = seed + 5000;
  }
  update() {
    // mece el anclaje suavemente (Calder)
    let dx = map(noise(this.nx), 0, 1, -14, 14);
    let dy = map(noise(this.ny), 0, 1, -8, 8);
    this.pos.x = this.base.x + dx;
    this.pos.y = this.base.y + dy;
    this.nx += 0.008; this.ny += 0.01;
  }
  show() {
    noStroke(); fill(230);
    circle(this.pos.x, this.pos.y, 6);
  }
}

class Rod {
  // resorte amortiguado entre anchor (punto) y body (masa)
  constructor(anchor, body, rest, k=0.08, c=0.18) {
    this.a = anchor; this.b = body;
    this.rest = rest; this.k = k; this.c = c;
  }
  apply() {
    let dir = p5.Vector.sub(this.b.pos, this.a.pos);
    let dist = max(0.0001, dir.mag());
    dir.mult(1/dist); // normaliza
    let x = dist - this.rest;
    // amortiguación: proyecta vel relativa sobre la dirección
    let relv = p5.Vector.dot(this.b.vel, dir); // anchor ~ 0
    let forceMag = (-this.k * x) + (-this.c * relv);
    let f = p5.Vector.mult(dir, forceMag);
    this.b.applyForce(f);
    // (anclaje fijo, no aplicamos reacción)
  }
  show() {
    stroke(220, 220, 230, 140); strokeWeight(1.5);
    line(this.a.pos.x, this.a.pos.y, this.b.pos.x, this.b.pos.y);
  }
}

// ---------- Helpers / interacción ----------
function addHangingBody() {
  if (anchors.length === 0) return;
  let a = random(anchors);
  // masa ~ N(1.2, 0.6) recortada
  let m = constrain(abs(randomGaussian(1.2, 0.6)), 0.4, 3.0);
  // longitud del brazo ~ N(90, 40) recortada
  let L = constrain(abs(randomGaussian(90, 40)), 40, 160);
  // coloca el body cerca del anclaje, hacia abajo
  let b = new Body(a.pos.x + random(-20, 20), a.pos.y + L + random(-10, 10), m);
  bodies.push(b);
  rods.push(new Rod(a, b, L));
}

function mousePressed() { dragStart = createVector(mouseX, mouseY); }
function mouseReleased() {
  if (!dragStart) return;
  // body libre con velocidad inicial del gesto
  let v = createVector(mouseX - dragStart.x, mouseY - dragStart.y).mult(0.06);
  let m = constrain(abs(randomGaussian(1.0, 0.5)), 0.4, 3.0);
  let b = new Body(dragStart.x, dragStart.y, m);
  b.vel.add(v);
  bodies.push(b);
  dragStart = null;
}

function keyPressed() {
  if (key === 'A' || key === 'a') {
    anchors.push(new Anchor(mouseX, height*0.16 + random(-10, 10), random(1e6)));
  }
  if (key === 'B' || key === 'b') addHangingBody();
  if (key === 'G' || key === 'g') G = max(0.2, G - 0.1);
  if (key === 'H' || key === 'h') G = min(5.0, G + 0.1);
  if (key === 'W' || key === 'w') useWind = !useWind;
  if (key === 'S' || key === 's') useSprings = !useSprings;
  if (key === ' ')               trail = !trail;
  if (key === 'C' || key === 'c') background(8);
  if (key === 'R' || key === 'r') { background(8); initScene(); }
}
```

**noise() (Perlin):**
1. para el viento que mece suavemente las placas;
2. para oscilar los anclajes (efecto Calder).

**randomGaussian() (distribución normal):**
1.	masas y tamaños de las placas (más valores cerca de la media → estética coherente);
2.	longitudes de los brazos (rest length) con pequeñas variaciones verosímiles.

**Enlace:** https://editor.p5js.org/TatoDesignn/sketches/2iDgHt0_v

<img width="846" height="558" alt="Captura de pantalla 2025-08-21 a la(s) 12 55 26 a m" src="https://github.com/user-attachments/assets/760ab524-8d27-45de-8f57-b64e6023318a" />
