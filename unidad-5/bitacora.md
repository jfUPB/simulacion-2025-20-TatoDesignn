# Evidencias de la unidad 5

## 📥Actividad 2

### ✅Ejemplo 4.2 — Array of Particles

1. Concepto aplicado (Unidad 4): oscilación senoidal por partícula (fase distinta por partícula) → “bamboleo” lateral natural sin Perlin.

2. **Qué cambié y por qué**
- Antes: caída vertical “en árbol”.
- Ahora: cada partícula tiene una fase y una frecuencia angular; aplico una fuerza lateral

3. Gestión de vida y memoria
- Emite 1 partícula por frame.
- Recorro el array de atrás hacia adelante y hago splice(i,1) si isDead(). Al eliminar la referencia, entra el GC

4. **Enlace al ejemplo 4.2 modificado:** https://editor.p5js.org/TatoDesignn/sketches/dpDfcTeuD

5. **Codigo Modificado:**
```javascript
let particles = [];
let t = 0;

function setup() {
  createCanvas(640, 240);
}

function draw() {
  background(255);
  // emitir desde arriba-centro
  particles.push(new SineDriftParticle(width/2, 20));

  for (let i = particles.length - 1; i >= 0; i--) {
    particles[i].run();
    if (particles[i].isDead()) particles.splice(i, 1);
  }
  t += 0.016;
}

class SineDriftParticle {
  constructor(x, y) {
    this.pos = createVector(x, y);
    this.vel = createVector(random(-0.8, 0.8), random(-2, -0.2));
    this.acc = createVector(0, 0);
    this.lifespan = 255;

    // parámetros de la oscilación (Unidad 4)
    this.A = random(0.02, 0.08);      // amplitud de fuerza lateral
    this.omega = random(1.2, 2.0);    // velocidad angular
    this.phi = random(TWO_PI);        // fase individual
  }

  run() {
    this.applySineLateral();
    this.update();
    this.show();
  }

  applySineLateral() {
    // gravedad + fuerza lateral senoidal (solo en X)
    let lateral = this.A * sin(this.omega * t + this.phi);
    this.applyForce(createVector(lateral, 0.05));
  }

  applyForce(f) { this.acc.add(f); }

  update() {
    this.vel.add(this.acc);
    this.pos.add(this.vel);
    this.acc.mult(0);
    this.lifespan -= 2;
  }

  show() {
    noStroke();
    fill(0, this.lifespan);
    circle(this.pos.x, this.pos.y, 12);
  }

  isDead() { return this.lifespan <= 0; }
}
```
6. **Captura de pantalla:**

<img width="598" height="232" alt="Captura de pantalla 2025-10-01 a la(s) 9 56 34 p m" src="https://github.com/user-attachments/assets/f9a93818-49c1-4abc-866c-1eb216886760" />

### ✅Ejemplo 4.4 — System of Systems

1. Concepto aplicado (Unidad 1): randomGaussian() para magnitud de velocidad inicial y tiempo de vida (distribución normal → más coherencia visual)

2. **Qué cambié y por qué**
- Varios emisores en pantalla que se crean con clic.
- Cada partícula nace con rapidez, muchas partículas “normales” y pocas muy rápidas/lentas, variación realista.

3. Gestión de vida y memoria
	•	Cada Emitter mantiene su arreglo. El run() borra hacia atrás si isDead().


4. **Enlace al ejemplo 4.4 modificado:** https://editor.p5js.org/TatoDesignn/sketches/cfQpPOOOz

5. **Codigo Modificado:**
```javascript
let emitters = [];

function setup() {
  createCanvas(640, 240);
  textSize(12);
}

function draw() {
  background(250);
  for (let e of emitters) {
    e.addParticle();
    e.run();
  }

  fill(0);
  text("Click para agregar emisores. Gauss en velocidad/vida.", 10, 20);
}

function mousePressed() {
  emitters.push(new Emitter(mouseX, mouseY));
}

class Emitter {
  constructor(x, y) {
    this.pos = createVector(x, y);
    this.particles = [];
  }
  addParticle() { this.particles.push(new GausParticle(this.pos)); }
  run() {
    for (let i = this.particles.length - 1; i >= 0; i--) {
      let p = this.particles[i];
      p.applyForce(createVector(0, 0.05)); // gravedad
      p.update();
      p.show();
      if (p.isDead()) this.particles.splice(i, 1);
    }
  }
}

class GausParticle {
  constructor(position) {
    this.pos = position.copy();
    // magnitud de velocidad ~ N(μ=2, σ=0.8), truncada
    let speed = constrain(abs(randomGaussian(2, 0.8)), 0.2, 4);
    let dir = p5.Vector.random2D();
    this.vel = dir.mult(speed);
    this.acc = createVector(0, 0);

    // lifespan ~ N(μ=220, σ=60), truncada
    this.lifespan = constrain(randomGaussian(220, 60), 120, 320);
    this.size = map(speed, 0.2, 4, 6, 12, true);
  }
  applyForce(f) { this.acc.add(f); }
  update() {
    this.vel.add(this.acc);
    this.pos.add(this.vel);
    this.acc.mult(0);
    this.lifespan -= 2;
  }
  show() {
    stroke(0, this.lifespan);
    fill(0, this.lifespan);
    circle(this.pos.x, this.pos.y, this.size);
  }
  isDead() { return this.lifespan <= 0; }
}
```

6. **Captura de pantalla:**

<img width="560" height="229" alt="Captura de pantalla 2025-10-01 a la(s) 10 05 10 p m" src="https://github.com/user-attachments/assets/90b00660-f45c-4c7e-8e5b-1a94d96d8c9f" />

### ✅ Ejemplo 4.5 — Inheritance & Polymorphism

1. Concepto aplicado (Unidad 2): orientación con heading() + translate()/rotate() (sprites apuntan según su velocidad).

2. **Qué cambié y por qué**
- Clase base BaseParticle + dos subtipos:
- ArrowParticle: triángulo que apunta con vel.heading().
- RingParticle: anillo expandiéndose.
- Fuerza de vórtice: componente tangencial alrededor del centro (giro suave).

3. **Gestión de vida y memoria**
- Emitter alterna tipos. Limpieza hacia atrás + splice.

4. **Enlace al ejemplo 4.5 modificado:** https://editor.p5js.org/TatoDesignn/sketches/l92hwNqCz

5. **Codigo Modificado:**
```javascript
let emitter;
let center;

function setup() {
  createCanvas(640, 240);
  center = createVector(width/2, height/2);
  emitter = new Emitter(width/2, 40);
}

function draw() {
  background(255);
  // vórtice: tangencial + leve atracción al centro
  emitter.addParticle();
  emitter.run();
}

class Emitter {
  constructor(x, y) {
    this.origin = createVector(x, y);
    this.particles = [];
    this.flip = false;
  }
  addParticle() {
    this.flip = !this.flip;
    if (this.flip) this.particles.push(new ArrowParticle(this.origin));
    else           this.particles.push(new RingParticle(this.origin));
  }
  run() {
    for (let i = this.particles.length-1; i>=0; i--) {
      let p = this.particles[i];
      // fuerzas: vórtice + atracción
      let toC = p5.Vector.sub(center, p.pos);
      let r = max(1, toC.mag());
      let radial = toC.copy().setMag(0.02);        // atracción tenue
      let tang = createVector(-toC.y, toC.x).setMag(0.03); // giro (perp)
      p.applyForce(radial.add(tang));
      p.applyForce(createVector(0, 0.05));         // gravedad ligera

      p.update();
      p.show();
      if (p.isDead()) this.particles.splice(i, 1);
    }
  }
}

class BaseParticle {
  constructor(origin) {
    this.pos = origin.copy();
    this.vel = p5.Vector.random2D().mult(random(0.5, 1.8));
    this.acc = createVector(0,0);
    this.lifespan = 255;
  }
  applyForce(f){ this.acc.add(f); }
  update(){
    this.vel.add(this.acc);
    this.pos.add(this.vel);
    this.acc.mult(0);
    this.lifespan -= 2;
  }
  isDead(){ return this.lifespan <= 0; }
  show(){ /* Override en subclasses */ }
}

class ArrowParticle extends BaseParticle {
  show() {
    push();
    translate(this.pos.x, this.pos.y);
    rotate(this.vel.heading());
    noStroke();
    fill(30, this.lifespan);
    triangle(10,0, -8,5, -8,-5);
    pop();
  }
}

class RingParticle extends BaseParticle {
  constructor(o){ super(o); this.r = 4; }
  update(){ super.update(); this.r += 0.25; }
  show(){
    noFill();
    stroke(0, this.lifespan);
    circle(this.pos.x, this.pos.y, this.r*2);
  }
}
```

6. **Captura de pantalla:**

<img width="388" height="231" alt="Captura de pantalla 2025-10-01 a la(s) 10 14 11 p m" src="https://github.com/user-attachments/assets/8f94c505-f44c-4e97-ae9d-84c2779f583e" />

### ✅ Ejemplo 4.6 — Particle System with Forces

1. Concepto aplicado (Unidad 3): arrastre cuadrático (drag ~ v²) con densidad controlada por mouse

2. **Qué cambié y por qué**
- En lugar de viento, modelo un medio: al presionar mouse, aumenta la densidad del fluido → mayor frenado (como caer en gel).

3. **Gestión de vida y memoria**
- Igual: borrar hacia atrás + splice.

4. **Enlace al ejemplo 4.6 modificado:** https://editor.p5js.org/TatoDesignn/sketches/U2tA2EuPe

5. **Codigo Modificado:**
```javascript
let emitter;

function setup() {
  createCanvas(1280, 480);
  emitter = new Emitter(width/2, 60);
}

function draw() {
  background(255, 30);

  // gravedad global
  let gravity = createVector(0, 0.08);
  emitter.applyForce(gravity);

  // densidad del medio depende del mouse (si presionado)
  let rho = mouseIsPressed ? map(mouseX, 0, width, 0.02, 0.3) : 0.0;
  emitter.applyQuadraticDrag(rho);

  emitter.addParticle();
  emitter.run();

  fill(0);
  textSize(14);
  text(`Densidad: ${rho.toFixed(3)} (mantén clic y mueve X)`, 10, height-10);
}

class Emitter {
  constructor(x, y) { this.origin=createVector(x,y); this.particles=[]; }
  addParticle(){ this.particles.push(new Particle(this.origin)); }
  applyForce(f){ for (let p of this.particles) p.applyForce(f); }
  applyQuadraticDrag(rho){
    if (rho <= 0) return;
    for (let p of this.particles) {
      let v = p.vel.copy();
      let speed = v.mag();
      if (speed === 0) continue;
      let dragMag = rho * speed * speed * 0.02;
      let drag = v.normalize().mult(-dragMag);
      p.applyForce(drag);
    }
  }
  run(){
    for (let i=this.particles.length-1; i>=0; i--){
      this.particles[i].run();
      if (this.particles[i].isDead()) this.particles.splice(i,1);
    }
  }
}

class Particle {
  constructor(origin){
    this.pos = origin.copy();
    this.vel = createVector(random(-1,1), random(-2,0));
    this.acc = createVector(0,0);
    this.lifespan = 255;
  }
  applyForce(f){ this.acc.add(f); }
  run(){ this.update(); this.show(); }
  update(){
    this.vel.add(this.acc);
    this.pos.add(this.vel);
    this.acc.mult(0);
    this.lifespan -= 2;
  }
  show(){
    stroke(0, this.lifespan);
    fill(127, this.lifespan);
    circle(this.pos.x, this.pos.y, 8);
  }
  isDead(){ return this.lifespan<0; }
}
```
6. **Captura de pantalla:**

<img width="226" height="235" alt="Captura de pantalla 2025-10-01 a la(s) 10 44 07 p m" src="https://github.com/user-attachments/assets/d13caaa1-f272-4461-b5ec-5337c2c3083e" />

### ✅ Ejemplo 4.7 — Particle System with a Repeller

1. Concepto aplicado (Unidades 1 + 4 combinadas)
- El repeller se mueve siguiendo una trayectoria Perlin (U1)
- Su fuerza oscila en el tiempo con una senoide (U4)

2. **Qué cambié y por qué**
- Mantengo la idea de repulsor, pero lo vuelvo vivo: su posición recorre una curva orgánica y su “pulsación” sube/baja periódicamente.

3. Gestión de vida y memoria
- Igual que anteriores: limpiar hacia atrás + splice.

4. **Enlace al ejemplo 4.6 modificado:** https://editor.p5js.org/TatoDesignn/sketches/PZ5Ff6G5p

5. **Codigo Modificado:**
```javascript
let emitter, rep;
let t = 0;

function setup() {
  createCanvas(640, 240);
  emitter = new Emitter(width/2, 40);
  rep = new MovingRepeller();
}

function draw() {
  background(255);
  emitter.addParticle();

  // gravedad suave
  emitter.applyForce(createVector(0, 0.08));
  // repulsor móvil y pulsante
  rep.update(t);
  emitter.applyRepeller(rep);

  emitter.run();
  rep.show();

  t += 0.01;
}

class Emitter {
  constructor(x,y){ this.origin=createVector(x,y); this.particles=[]; }
  addParticle(){ this.particles.push(new Particle(this.origin)); }
  applyForce(f){ for (let p of this.particles) p.applyForce(f); }
  applyRepeller(r){
    for (let p of this.particles) {
      let dir = p5.Vector.sub(p.pos, r.pos);
      let d2 = max(16, dir.magSq());
      dir.normalize();
      let k = r.strength(); // variable en el tiempo (seno)
      let f = dir.mult(k / d2);
      p.applyForce(f);
    }
  }
  run(){
    for (let i=this.particles.length-1;i>=0;i--){
      let p=this.particles[i];
      p.run();
      if (p.isDead()) this.particles.splice(i,1);
    }
  }
}

class Particle {
  constructor(o){
    this.pos = o.copy();
    this.vel = createVector(random(-1,1), random(-2,0));
    this.acc = createVector(0,0);
    this.lifespan = 255;
  }
  applyForce(f){ this.acc.add(f); }
  run(){ this.update(); this.show(); }
  update(){ this.vel.add(this.acc); this.pos.add(this.vel); this.acc.mult(0); this.lifespan-=2; }
  show(){ stroke(0,this.lifespan); fill(127,this.lifespan); circle(this.pos.x,this.pos.y,8); }
  isDead(){ return this.lifespan<0; }
}

class MovingRepeller {
  constructor(){
    this.pos = createVector(width*0.7, height*0.6);
    this.nx = random(1000); this.ny = random(2000);
    this.K0 = 1200;    // base
    this.AK = 800;     // amplitud de oscilación
    this.omega = 1.2;  // velocidad angular de la senoide
  }
  update(t){
    // Perlin path
    let x = noise(this.nx) * width;
    let y = noise(this.ny) * height*0.8 + height*0.1;
    this.pos.set(x, y);
    this.nx += 0.005; this.ny += 0.006;
    this._k = this.K0 + this.AK * sin(this.omega * t); // k(t)
  }
  strength(){ return max(100, this._k); }
  show(){
    noFill(); stroke(220,0,0,160); strokeWeight(2);
    circle(this.pos.x, this.pos.y, 24);
  }
}
```
6. **Captura de pantalla:**

<img width="313" height="172" alt="Captura de pantalla 2025-10-01 a la(s) 10 50 35 p m" src="https://github.com/user-attachments/assets/7925f80e-2794-421d-995c-8bff67f45b41" />







