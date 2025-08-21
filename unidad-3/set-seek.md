# Unidad 3

## 🔎 Fase: Set + Seek (No entregó, esta nota la deja el profesor)

### 📥Actividad 9:

**Friccion:**

Emitir partículas desde el mouse como si fuera “fuegos artificiales” que se desliza y se va frenando por fricción, dejando una estela que se disipa.

Fricción cinética: opuesta a la velocidad, magnitud constante. Si la velocidad es muy baja, paro la partícula fricción estática.

```javascript
// Fricción — "Polvo sobre lienzo"
let emitter;
let mu = 0.06;   // coef. fricción
let N = 1;
let particles = [];

function setup() {
  createCanvas(960, 540);
  background(10);
  emitter = createVector(width/2, height/2);
}

function draw() {
  // velo suave
  noStroke(); fill(10, 20); rect(0,0,width,height);

  // emitir si presionado
  if (mouseIsPressed) {
    emitter.set(mouseX, mouseY);
    for (let i = 0; i < 8; i++) {
      let v = p5.Vector.random2D().mult(random(1, 4));
      particles.push(new Particle(emitter.x, emitter.y, v));
    }
  }

  // actualizar
  for (let p of particles) {
    // fricción
    p.applyFriction(mu, N);
    p.update();
    p.edgesWrap();
    p.show();
  }

  // limpiar muertos
  particles = particles.filter(p => p.alive());

  // HUD
  fill(255); noStroke();
  text(`particles: ${particles.length} — μ: ${mu.toFixed(2)} (1/2 ajusta) — arrastra para emitir — C: clear — R: reset`,
       12, height-12);
}

class Particle {
  constructor(x,y,vel) {
    this.pos = createVector(x,y);
    this.vel = vel.copy();
    this.acc = createVector(0,0);
    this.size = random(2, 5);
    this.life = 255;
    this.baseCol = color(random(180,255), random(160,220), 255);
  }
  applyForce(f){ this.acc.add(f); }
  applyFriction(mu, N){
    let speed = this.vel.mag();
    if (speed < 0.05) { this.vel.set(0,0); return; }
    let f = this.vel.copy().normalize().mult(-mu*N);
    this.applyForce(f);
  }
  update(){
    this.vel.add(this.acc);
    this.pos.add(this.vel);
    this.acc.mult(0);
    // desvanecer por tiempo y velocidad
    this.life -= map(this.vel.mag(), 0, 4, 1, 3, true);
  }
  show(){
    noStroke();
    fill(red(this.baseCol), green(this.baseCol), blue(this.baseCol), this.life);
    circle(this.pos.x, this.pos.y, this.size);
  }
  edgesWrap(){
    if (this.pos.x>width) this.pos.x=0;
    if (this.pos.x<0) this.pos.x=width;
    if (this.pos.y>height) this.pos.y=0;
    if (this.pos.y<0) this.pos.y=height;
  }
  alive(){ return this.life > 8; }
}

function keyPressed(){
  if (key==='1') mu = max(0, mu-0.01);
  if (key==='2') mu = min(0.5, mu+0.01);
  if (key==='C' || key==='c') { background(10); }
  if (key==='R' || key==='r') { particles = []; background(10); }
}
```
**Enlace:** https://editor.p5js.org/TatoDesignn/sketches/nnadqL8fN

<img width="795" height="569" alt="Captura de pantalla 2025-08-20 a la(s) 11 05 37 p m" src="https://github.com/user-attachments/assets/7998222d-4b0f-4a56-8510-b16a39816430" />


**Resistencia de aire y fluidos**

Una lluvia de partículas cae: en aire hay arrastre lineal (suave), y al entrar al charco (zona inferior) sufren arrastre cuadrático fuerte; cambian ritmo y densidad visual.

```javascript
// Resistencia aire/fluidos — "Ecos del Charco"
let drops = [];
let pondTop;
let rho = 0.05, Cd = 0.9, area = 8, kAir = 0.02;
let g = null, wind = 0;

function setup(){
  createCanvas(960, 540);
  pondTop = height*0.65;
  g = createVector(0, 0.22);
  background(8);
}

function draw(){
  // velo
  noStroke(); fill(8, 16); rect(0,0,width,height);
  // charco
  noStroke(); fill(40, 100, 120, 120); rect(0, pondTop, width, height-pondTop);

  // spawner
  if (frameCount % 3 === 0 && drops.length < 600) {
    for (let i=0;i<3;i++) drops.push(new Drop(random(width), -10));
  }

  wind *= 0.95;

  for (let d of drops){
    // gravedad
    d.applyForce(g);
    // viento
    d.applyForce(createVector(wind, 0));

    // arrastre
    let v = d.vel.copy();
    if (v.mag() > 0){
      if (d.pos.y < pondTop){
        // aire (lineal)
        let F = v.mult(-kAir);
        d.applyForce(F);
      } else {
        // fluido (cuadrático)
        let speed = v.mag();
        let dir = v.copy().normalize().mult(-1);
        let mag = 0.5 * rho * Cd * area * speed * speed;
        d.applyForce(dir.mult(mag));
      }
    }

    d.update();
    d.wrapTop();
    d.show( d.pos.y < pondTop ? color(190,220,255,220) : color(100,230,200,220) );
  }

  // HUD
  fill(255); noStroke();
  text(`drops: ${drops.length} — ρ: ${rho.toFixed(3)} (←/→) — pondTop: ${int(pondTop)} (↑/↓) — W viento — click: chorros — C clear`,
       12, height-12);
}

class Drop{
  constructor(x,y){
    this.pos = createVector(x,y);
    this.vel = createVector(random(-0.2,0.2), random(0.4,1.2));
    this.acc = createVector(0,0);
    this.size = random(2,4.5);
    this.life = 255;
    this.prev = this.pos.copy();
  }
  applyForce(f){ this.acc.add(f); }
  update(){
    this.vel.add(this.acc);
    this.prev.set(this.pos);
    this.pos.add(this.vel);
    this.acc.mult(0);
    this.life -= 0.6;
  }
  wrapTop(){
    if (this.pos.x<0) this.pos.x=width;
    if (this.pos.x>width) this.pos.x=0;
    if (this.pos.y>height){ this.pos.set(random(width), -10); this.prev.set(this.pos); this.vel.set(0, random(0.4,1.2)); this.life=255; }
  }
  show(col){
    noStroke(); fill(col);
    circle(this.pos.x, this.pos.y, this.size);
  }
}

function mousePressed(){
  for (let i=0;i<40;i++) drops.push(new Drop(mouseX + random(-10,10), mouseY + random(-10,10)));
}
function keyPressed(){
  if (keyCode===LEFT_ARROW)  rho = max(0, rho-0.005);
  if (keyCode===RIGHT_ARROW) rho = min(0.3, rho+0.005);
  if (keyCode===UP_ARROW)    pondTop = max(40, pondTop-20);
  if (keyCode===DOWN_ARROW)  pondTop = min(height-20, pondTop+20);
  if (key==='W' || key==='w') wind += random(-0.6,0.6);
  if (key==='C' || key==='c') { background(8); }
}
```

**Enlace:** https://editor.p5js.org/TatoDesignn/sketches/0SPfd4ZQg

<img width="843" height="541" alt="Captura de pantalla 2025-08-20 a la(s) 11 08 37 p m" src="https://github.com/user-attachments/assets/41e211ab-b8c6-4bde-b077-459360971a93" />

**Atracción gravitacional**

Un enjambre de partículas navega hacia un punto azul con uno atractores. al llegar las particulas se vuelven órbitas, capturas y escapes. Visualmente parece una constelacion.

```javascript
// Atracción — "Enjambre Orbital"
let swarm = [];
let wells = [];
let G = 1.2, eps = 80;
let trail = true;
let dragStart = null;

function setup(){
  createCanvas(960,540);
  background(8);
  wells.push(new Well(width*0.5, height*0.5, 26, 2200));
  // arranque con un enjambre
  launchGroup(width*0.3, height*0.6, createVector(3, -1));
}

function draw(){
  if (trail) { noStroke(); fill(8, 14); rect(0,0,width,height); }
  else background(8);

  // wells
  for (let w of wells) w.show();

  // swarm
  for (let s of swarm){
    for (let w of wells){
      s.applyForce(w.attract(s));
    }
    s.update();
    s.wrap();
    s.show();
  }

  // guía lanzamiento
  if (dragStart){
    stroke(255,180); strokeWeight(2);
    line(dragStart.x, dragStart.y, mouseX, mouseY);
    noStroke(); fill(255); circle(dragStart.x, dragStart.y, 4);
  }

  // HUD
  noStroke(); fill(255);
  text(`particles: ${swarm.length} — wells: ${wells.length} — G: ${G.toFixed(2)} (G/H) — A add well — drag to launch — T trail — C clear`, 12, height-12);
}

class Well{
  constructor(x,y,r,m){ this.pos=createVector(x,y); this.r=r; this.m=m; }
  show(){ noStroke(); fill(90,130,255,180); circle(this.pos.x,this.pos.y, this.r*2); }
  attract(s){
    let dir = p5.Vector.sub(this.pos, s.pos);
    let distSq = dir.magSq() + eps;
    dir.normalize();
    let strength = (G * this.m * s.m) / distSq;
    return dir.mult(strength);
  }
}

class Body{
  constructor(x,y,vx,vy,m=4){
    this.pos=createVector(x,y);
    this.vel=createVector(vx,vy);
    this.acc=createVector(0,0);
    this.m=m;
    this.prev=this.pos.copy();
    this.col = color(random(180,255), random(180,255), random(120,220), 200);
    this.size = random(2.2, 3.6);
    this.maxSpeed = 28;
  }
  applyForce(f){ this.acc.add(p5.Vector.div(f, this.m)); }
  update(){
    this.vel.add(this.acc).limit(this.maxSpeed);
    this.prev.set(this.pos);
    this.pos.add(this.vel);
    this.acc.mult(0);
  }
  wrap(){
    if (this.pos.x>width){ this.pos.x=0; this.prev.set(this.pos); }
    if (this.pos.x<0){ this.pos.x=width; this.prev.set(this.pos); }
    if (this.pos.y>height){ this.pos.y=0; this.prev.set(this.pos); }
    if (this.pos.y<0){ this.pos.y=height; this.prev.set(this.pos); }
  }
  show(){
    // punto
    noStroke(); fill(this.col); circle(this.pos.x, this.pos.y, this.size);
  }
}

function launchGroup(x,y,vel){
  for (let i=0;i<60;i++){
    let jitter = p5.Vector.random2D().mult(random(0.2, 1.2));
    swarm.push(new Body(x+random(-12,12), y+random(-12,12), vel.x+jitter.x, vel.y+jitter.y));
  }
}

function mousePressed(){ dragStart = createVector(mouseX, mouseY); }
function mouseReleased(){
  if (dragStart){
    let v = createVector(mouseX - dragStart.x, mouseY - dragStart.y).mult(0.05);
    launchGroup(dragStart.x, dragStart.y, v);
    dragStart = null;
  }
}
function keyPressed(){
  if (key==='A' || key==='a') wells.push(new Well(mouseX, mouseY, 22, 1600));
  if (key==='G' || key==='g') G = max(0.2, G-0.1);
  if (key==='H' || key==='h') G = min(5, G+0.1);
  if (key==='T' || key==='t') trail = !trail;
  if (key==='C' || key==='c'){ background(8); swarm=[]; }
}
```

**Enlace:** https://editor.p5js.org/TatoDesignn/sketches/vPI1l6RRa

<img width="844" height="557" alt="Captura de pantalla 2025-08-20 a la(s) 11 14 53 p m" src="https://github.com/user-attachments/assets/f22a703c-c78d-4ac3-9e83-a60842ec79d4" />


