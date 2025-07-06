


<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>✨ Mahran's Animation Showcase ✨</title>
  <style>
    body, html { margin: 0; padding: 0; overflow: hidden; background: #000; }
    canvas { display: block; }
    #book {
      position: absolute; bottom: 50px; left: 50%;
      width: 200px; height: 150px;
      margin-left: -100px;
      background: #444;
      transform-origin: left center;
      animation: openBook 3s forwards;
    }
    @keyframes openBook {
      0% { transform: scaleX(0); }
      100% { transform: scaleX(1); }
    }
    #ball {
      position: absolute; bottom: 200px; left: 50%;
      width: 40px; height: 40px;
      margin-left: -20px;
      background: red;
      border-radius: 50%;
      animation: bounce 2s infinite ease-in-out;
    }
    @keyframes bounce {
      0%,100% { transform: translateY(0); }
      50% { transform: translateY(-200px); }
    }
  </style>
</head>
<body>
  <div id="book"></div>
  <div id="ball"></div>
  <canvas id="canvas"></canvas>

  <script>
    // 🎆 Fireworks + stars on canvas
    const c = document.getElementById('canvas');
    const ctx = c.getContext('2d');
    c.width = window.innerWidth;
    c.height = window.innerHeight;

    window.addEventListener('resize', () => {
      c.width = window.innerWidth;
      c.height = window.innerHeight;
    });

    const stars = [];
    for (let i = 0; i < 200; i++) {
      stars.push({ x: Math.random()*c.width, y: Math.random()*c.height, r: Math.random()*1.5, alpha: Math.random() });
    }

    class Firework {
      constructor() {
        this.reset();
      }
      reset() {
        this.x = Math.random()*c.width;
        this.y = c.height + 10;
        this.vx = (Math.random()-0.5)*2;
        this.vy = - (8 + Math.random()*4);
        this.life = 0;
        this.exploded = false;
        this.particles = [];
      }
      update() {
        if (!this.exploded) {
          this.x += this.vx;
          this.y += this.vy;
          this.vy += 0.1;
          if (this.vy >= 0) {
            this.exploded = true;
            const count = 30 + Math.random()*30;
            for (let i = 0; i < count; i++) {
              const angle = Math.random()*Math.PI*2;
              const speed = 1 + Math.random()*4;
              this.particles.push({
                x: this.x, y: this.y,
                vx: Math.cos(angle)*speed, vy: Math.sin(angle)*speed,
                alpha: 1, life: 30 + Math.random()*30
              });
            }
          }
        } else {
          this.particles.forEach(p => {
            p.x += p.vx;
            p.y += p.vy;
            p.vy += 0.05;
            p.alpha -= 0.02;
            p.life--;
          });
          this.particles = this.particles.filter(p => p.life > 0);
          if (this.particles.length == 0) this.reset();
        }
      }
      draw() {
        if (!this.exploded) {
          ctx.beginPath();
          ctx.arc(this.x, this.y, 2, 0, Math.PI*2);
          ctx.fillStyle = '#fff';
          ctx.fill();
        } else {
          this.particles.forEach(p => {
            ctx.beginPath();
            ctx.arc(p.x, p.y, 2, 0, Math.PI*2);
            ctx.fillStyle = `rgba(255,${Math.floor(Math.random()*255)},0,${p.alpha})`;
            ctx.fill();
          });
        }
      }
    }

    const fireworks = [];
    for (let i=0;i<5;i++) fireworks.push(new Firework());

    function animate() {
      ctx.fillStyle = 'rgba(0,0,0,0.2)';
      ctx.fillRect(0,0,c.width,c.height);

      // stars
      stars.forEach(s => {
        s.alpha += (Math.random()-0.5)*0.02;
        s.alpha = Math.max(0.1, Math.min(1, s.alpha));
        ctx.beginPath();
        ctx.arc(s.x,s.y,s.r,0,Math.PI*2);
        ctx.fillStyle = `rgba(255,255,255,${s.alpha})`;
        ctx.fill();
      });

      fireworks.forEach(f => { f.update(); f.draw(); });

      requestAnimationFrame(animate);
    }

    animate();
  </script>
</body>
</html>
