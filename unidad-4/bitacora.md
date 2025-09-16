# Evidencias de la unidad 4

## Explicación conceptual de la obra

* ¿Qué concepto de la unidad 4 y cómo lo aplicaste en la obra?
> 
>El concepto central fue el uso de funciones sinusoides y ondas. Lo apliqué para que los anclajes de las placas se movieran como si estuvieran meciéndose con una ola senoidal viajera. Cada anclaje oscila en fase con respecto a los demás, generando la sensación de una “marea” que recorre todo el sistema, evocando el movimiento fluido y repetitivo de un móvil cinético.

* ¿Qué concepto de la unidad 3 y cómo lo aplicaste en la obra?
> Usé el modelado de fuerzas: la gravedad mutua (n-cuerpos) para que las placas se atraigan entre sí, los resortes amortiguados para mantenerlas unidas a sus anclajes, y además un viento Perlin como fuerza externa. Todas estas fuerzas se acumulan en cada frame y afectan la aceleración, siguiendo la segunda ley de Newton.
>

* ¿Qué concepto de la unidad 2 y cómo lo aplicaste en la obra?
> Apliqué el trabajo con vectores, ángulos y coordenadas. Cada placa calcula su dirección con velocity.heading(), y se dibuja un triángulo orientado con translate() y rotate() para que siempre apunte en la dirección de su movimiento. Además, toda la simulación está construida sobre la manipulación de vectores (p5.Vector) para posición, velocidad y aceleración.
>

* ¿Qué concepto de la unidad 1 y cómo lo aplicaste en la obra?
> Implementé varias formas de aleatoriedad más allá de random():
randomGaussian() para masas, tamaños y longitudes de varillas, lo que da naturalidad.
noise() para crear un viento suave y orgánico.
Impulsos Lévy como eventos raros que afectan la velocidad de algunos cuerpos, produciendo movimientos inesperados y expresivos.
>

## ¿Cómo resolviste la interacción?
> La interacción se diseñó para que el usuario pueda crear y transformar el sistema en tiempo real:
Arrastrar y soltar con el mouse: genera una nueva placa libre lanzada con la velocidad del gesto.
Tecla A: agrega un nuevo anclaje que se integra a la ola senoidal.
Tecla B: cuelga una nueva placa de un anclaje aleatorio.
Tecla W/S/N: alternan viento, resortes y saltos Lévy.
Teclas G/H, Z/X, ,/.: ajustan gravedad, amplitud y frecuencia de la ola.
Espacio: alterna el modo de estelas.
C/R: limpiar la pantalla o reiniciar la escena.
>

## Enlace a la obra en el editor de p5.js

[Aquí está mi obra](https://editor.p5js.org/TatoDesignn/sketches/2iDgHt0_v)

## Código de la obra 

``` js

let bodies = [];
let anchors = [];
let rods = [];

let G = 1.1;
let EPS = 100;            // suavizado gravitacional
let useWind = true;
let useSprings = true;
let useLevy = true;
let trail = true;

// OLA seno (Unidad 4)
let waveAmp = 14;         // amplitud (px)
let waveOmega = 0.7;      // velocidad angular (rad/s)
let wavePhase = 0;        // fase global, avanza con t
let waveK = 0.012;        // número de onda (fase espacial)
let t = 0;

const WIND_AMP = 0.12;
const WIND_SCALE = 0.0022;
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

  // anclajes iniciales (3-5 columnas)
  let cols = floor(random(3, 6));
  for (let i = 0; i < cols; i++) {
    let ax = map(i, 0, cols-1, width*0.15, width*0.85);
    anchors.push(new Anchor(ax, height*0.18 + random(-6, 6), random(1e6)));
  }

  // cuelga algunas placas
  for (let i = 0; i < 10; i++) addHangingBody();

  // lanza un grupito libre
  for (let i = 0; i < 12; i++) {
    let x = random(width*0.35, width*0.65);
    let y = random(height*0.35, height*0.65);
    let v = p5.Vector.random2D().mult(random(1, 3));
    let m = constrain(abs(randomGaussian(1.0, 0.5)), 0.4, 3.0);
    let b = new Body(x, y, m);
    b.vel.add(v);
    bodies.push(b);
  }
}

function draw() {
  if (trail) { noStroke(); fill(8, 16); rect(0,0,width,height); }
  else background(8);

  // OLA: mece anclajes como una marea que viaja
  wavePhase += waveOmega * 0.016; // dt aprox 1/60
  for (let a of anchors) a.updateWave(waveAmp, waveK, wavePhase);

  // Fuerzas n-cuerpos (i<j para eficiencia) — 3ª ley
  for (let i = 0; i < bodies.length; i++) {
    for (let j = i+1; j < bodies.length; j++) {
      let f = gravity(bodies[i], bodies[j]);
      bodies[i].applyForce(f);
      bodies[j].applyForce(p5.Vector.mult(f, -1));
    }
  }

  // Resortes amortiguados (varillas)
  if (useSprings) for (let r of rods) r.apply();

  // Viento Perlin
  if (useWind) {
    for (let b of bodies) {
      let ang = noise(b.pos.x*WIND_SCALE, b.pos.y*WIND_SCALE, t*0.4) * TAU;
      let wind = p5.Vector.fromAngle(ang).mult(WIND_AMP);
      b.applyForce(wind);
    }
  }

  // Impulsos Lévy esporádicos (evento raro, sobre la velocidad)
  if (useLevy && random() < 0.006) {
    let b = random(bodies);
    let theta = random(TWO_PI);
    // heavy-tail truncada
    let step = pow(max(1e-6, random()), -0.8) * 8;
    let impulse = p5.Vector.fromAngle(theta).mult(step * 0.25);
    b.vel.add(impulse);
  }

  // Integración y dibujo
  for (let b of bodies) {
    // arrastre suave dependiente de la velocidad (aire)
    let kAir = 0.015;
    b.applyForce(b.vel.copy().mult(-kAir));

    b.update();
    b.wrap();
    b.show();
  }

  // dibujar resortes y anclajes
  for (let r of rods) r.show();
  for (let a of anchors) a.show();

  // guía de lanzamiento
  if (dragStart) {
    stroke(255,200); strokeWeight(2);
    line(dragStart.x, dragStart.y, mouseX, mouseY);
    noStroke(); fill(255); circle(dragStart.x, dragStart.y, 4);
  }

  // HUD
  noStroke(); fill(255);
  text(
    `Bodies: ${bodies.length}  Anchors: ${anchors.length}  Rods: ${rods.length}  ` +
    `G: ${G.toFixed(2)} (G/H)  WaveAmp: ${waveAmp.toFixed(1)} (Z/X)  ` +
    `WaveFreq: ${waveOmega.toFixed(2)} (,/.)  Wind:${useWind?'ON':'OFF'} (W)  ` +
    `Springs:${useSprings?'ON':'OFF'} (S)  Lévy:${useLevy?'ON':'OFF'} (N)  ` +
    `SPACE: trail  C: clear  R: reset  Drag: launch`,
    12, height - 12
  );

  t += 0.01;
}

// -------- Física ----------
function gravity(b1, b2) {
  let dir = p5.Vector.sub(b2.pos, b1.pos);
  let distSq = dir.magSq() + EPS;    // suavizado
  dir.normalize();
  let strength = (G * b1.m * b2.m) / distSq;
  return dir.mult(strength);         // fuerza sobre b1 hacia b2
}

// -------- Clases ----------
class Body {
  constructor(x, y, m) {
    this.pos = createVector(x, y);
    this.vel = p5.Vector.random2D().mult(random(0.4, 1.6));
    this.acc = createVector(0, 0);
    this.m   = m;
    // tamaño → mapeado por masa con leve “respiración” senoidal (Unidad 4)
    this.baseSize = map(this.m, 0.4, 3.0, 6, 18, true);
    this.prev = this.pos.copy();

    // paleta con ligera gaussiana en tono
    let baseH = 210 + randomGaussian(0, 18);
    this.col  = color(`hsla(${baseH}, 70%, 65%, 0.95)`);
    this.maxSpeed = 28;
    this.phase = random(TAU); // fase para respiración individual
  }
  applyForce(f) { this.acc.add(p5.Vector.div(f, this.m)); }
  update() {
    this.vel.add(this.acc).limit(this.maxSpeed);
    this.prev.set(this.pos);
    this.pos.add(this.vel);
    this.acc.mult(0); // reset acumulación (Motion 101 + fuerzas)
  }
  wrap() {
    if (this.pos.x > width)  { this.pos.x = 0;    this.prev.set(this.pos); }
    if (this.pos.x < 0)      { this.pos.x = width;this.prev.set(this.pos); }
    if (this.pos.y > height) { this.pos.y = 0;    this.prev.set(this.pos); }
    if (this.pos.y < 0)      { this.pos.y = height;this.prev.set(this.pos); }
  }
  show() {
    // estela
    strokeWeight(1.2);
    let s = sin(t*1.2 + this.phase)*0.5 + 0.5; // 0..1
    let a = 110 + 100*s;
    stroke(red(this.col), green(this.col), blue(this.col), a);
    line(this.prev.x, this.prev.y, this.pos.x, this.pos.y);

    // triángulo indicador orientado a heading (Unidad 2)
    push();
    translate(this.pos.x, this.pos.y);
    let ang = this.vel.heading(); // dirección del movimiento
    rotate(ang);
    noStroke();
    fill(this.col);
    let size = this.baseSize * (0.85 + 0.25*s); // “respira”
    triangle(size, 0, -size*0.6, size*0.5, -size*0.6, -size*0.5);
    pop();
  }
}

class Anchor {
  constructor(x, y, seed) {
    this.base = createVector(x, y);
    this.pos  = this.base.copy();
    this.seed = seed;
  }
  updateWave(amp, k, phase) {
    // Ola viajera: desplazamiento vertical senoidal con fase espacial
    let theta = phase + this.base.x * k;
    // suma pequeña de noise para organicidad
    let dy = amp * sin(theta) + map(noise(this.seed + t*0.7), 0,1, -3, 3);
    this.pos.x = this.base.x;
    this.pos.y = this.base.y + dy;
  }
  show() {
    noStroke(); fill(230);
    circle(this.pos.x, this.pos.y, 6);
  }
}

class Rod {
  // resorte amortiguado entre anchor y body
  constructor(anchor, body, rest, k=0.08, c=0.18) {
    this.a = anchor; this.b = body;
    this.rest = rest; this.k = k; this.c = c;
  }
  apply() {
    let d = p5.Vector.sub(this.b.pos, this.a.pos);
    let L = max(0.0001, d.mag());
    let dir = d.copy().mult(1/L);
    let x = L - this.rest;
    let relv = p5.Vector.dot(this.b.vel, dir); // proyección de vel sobre la varilla
    let F = (-this.k * x) + (-this.c * relv);
    this.b.applyForce(dir.mult(F));
  }
  show() {
    stroke(220, 220, 230, 140); strokeWeight(1.5);
    line(this.a.pos.x, this.a.pos.y, this.b.pos.x, this.b.pos.y);
  }
}

// -------- Utilidades / interacción ----------
function addHangingBody() {
  if (anchors.length === 0) return;
  let a = random(anchors);
  let m = constrain(abs(randomGaussian(1.2, 0.6)), 0.4, 3.0);
  let L = constrain(abs(randomGaussian(100, 40)), 50, 180);
  let b = new Body(a.pos.x + random(-20, 20), a.pos.y + L + random(-12, 12), m);
  bodies.push(b);
  rods.push(new Rod(a, b, L));
}

function mousePressed()  { dragStart = createVector(mouseX, mouseY); }
function mouseReleased() {
  if (!dragStart) return;
  let v = createVector(mouseX - dragStart.x, mouseY - dragStart.y).mult(0.06);
  let m = constrain(abs(randomGaussian(1.0, 0.5)), 0.4, 3.0);
  let b = new Body(dragStart.x, dragStart.y, m);
  b.vel.add(v);
  bodies.push(b);
  dragStart = null;
}

function keyPressed() {
  if (key==='A' || key==='a') anchors.push(new Anchor(mouseX, height*0.16 + random(-10, 10), random(1e6)));
  if (key==='B' || key==='b') addHangingBody();

  if (key==='G' || key==='g') G = max(0.2, G - 0.1);
  if (key==='H' || key==='h') G = min(5.0, G + 0.1);

  if (key==='W' || key==='w') useWind = !useWind;
  if (key==='S' || key==='s') useSprings = !useSprings;
  if (key==='N' || key==='n') useLevy = !useLevy;

  if (key==='Z' || key==='z') waveAmp = max(0, waveAmp - 1);
  if (key==='X' || key==='x') waveAmp = min(60, waveAmp + 1);
  if (key===',' ) waveOmega = max(0, waveOmega - 0.05);
  if (key==='.' ) waveOmega = min(3, waveOmega + 0.05);

  if (key===' ') trail = !trail;
  if (key==='C' || key==='c') background(8);
  if (key==='R' || key==='r') { background(8); initScene(); }
}

```

## Captura de pantalla representativa


<img width="958" height="540" alt="Captura de pantalla 2025-09-16 a la(s) 2 29 01 p m" src="https://github.com/user-attachments/assets/7ef32c37-f067-4801-b55d-2d7912464861" />






