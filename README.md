# vibecoding2025
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Vibe Coding Web</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <style>
    body {
      margin: 0;
      background: black;
      color: white;
      font-family: sans-serif;
      text-align: center;
      overflow: hidden;
    }
    h1 {
      margin-top: 60px;
      animation: fadeIn 2s ease-in;
    }
    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(20px); }
      to { opacity: 1; transform: translateY(0); }
    }
    #particle-bg {
      position: fixed;
      top: 0; left: 0;
      width: 100vw; height: 100vh;
      z-index: -1;
    }
    #infoPanel {
      margin-top: 20px;
    }
    button {
      margin-top: 20px;
      padding: 12px 20px;
      font-size: 16px;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      background: #1e90ff;
      color: white;
      transition: transform 0.1s ease;
    }
    button:active {
      transform: scale(1.05);
    }
    #weather, #clock, #clickCounter {
      margin-top: 10px;
    }
    audio {
      display: none;
    }
  </style>
</head>
<body>
  <canvas id="particle-bg"></canvas>
  <h1>Welcome to Your Vibe Coding Experience 🌌</h1>
  <div id="infoPanel">
    <div id="clock">Time: --:--:--</div>
    <div id="weather">Weather: Loading...</div>
    <div id="clickCounter">You've clicked 0 times!</div>
  </div>
  <button onclick="handleClick()">Click Me!</button>
  <button onclick="toggleMusic()">🎵 Play/Pause Music</button>
  <audio id="bgMusic" loop>
    <source src="https://www.bensound.com/bensound-music/bensound-sunny.mp3" type="audio/mpeg">
  </audio>

  <script>
    // === 時鐘 ===
    function updateClock() {
      const now = new Date();
      const time = now.toLocaleTimeString();
      document.getElementById('clock').textContent = `Time: ${time}`;
    }
    setInterval(updateClock, 1000);
    updateClock();

    // === 天氣 API ===
    async function getWeather() {
      if (!navigator.geolocation) {
        document.getElementById('weather').textContent = 'Geolocation not supported.';
        return;
      }

      navigator.geolocation.getCurrentPosition(async pos => {
        const { latitude, longitude } = pos.coords;
        const apiKey = 'YOUR_API_KEY_HERE';
        const url = `https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&units=metric&appid=${apiKey}`;
        try {
          const res = await fetch(url);
          const data = await res.json();
          const temp = data.main.temp.toFixed(1);
          const city = data.name;
          const desc = data.weather[0].description;
          document.getElementById('weather').textContent = `Weather in ${city}: ${temp}°C, ${desc}`;
        } catch (e) {
          document.getElementById('weather').textContent = 'Failed to fetch weather.';
        }
      });
    }
    getWeather();

    // === 音樂控制 ===
    const music = document.getElementById('bgMusic');
    function toggleMusic() {
      if (music.paused) {
        music.play();
      } else {
        music.pause();
      }
    }

    // === 點擊計數器 ===
    let count = 0;
    function handleClick() {
      count++;
      document.getElementById('clickCounter').textContent = `You've clicked ${count} times!`;
    }

    // === 粒子背景 ===
    const canvas = document.getElementById('particle-bg');
    const ctx = canvas.getContext('2d');
    let particles = [];
    resizeCanvas();
    window.addEventListener('resize', resizeCanvas);

    function resizeCanvas() {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
    }

    class Particle {
      constructor() {
        this.x = Math.random() * canvas.width;
        this.y = Math.random() * canvas.height;
        this.vx = Math.random() * 0.4 - 0.2;
        this.vy = Math.random() * 0.4 - 0.2;
        this.radius = Math.random() * 2 + 1;
      }
      update() {
        this.x += this.vx;
        this.y += this.vy;
        if (this.x < 0 || this.x > canvas.width) this.vx *= -1;
        if (this.y < 0 || this.y > canvas.height) this.vy *= -1;
      }
      draw() {
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
        ctx.fillStyle = 'white';
        ctx.fill();
      }
    }

    function initParticles() {
      for (let i = 0; i < 120; i++) {
        particles.push(new Particle());
      }
    }

    function animate() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      particles.forEach(p => {
        p.update();
        p.draw();
      });
      requestAnimationFrame(animate);
    }

    initParticles();
    animate();
  


</script>
</body>
</html>
