# Eid-card 
<!DOCTYPE html>
<html>
<head>
  <style>
    body {
      margin: 0;
      padding: 0;
      background: url('https://i.imgur.com/yxIu94x.jpg') no-repeat center center fixed;
      background-size: cover;
      font-family: sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      color: white;
      overflow: hidden;
    }

    .container {
      text-align: center;
      background-color: rgba(0, 0, 0, 0.5);
      padding: 30px;
      border-radius: 12px;
      box-shadow: 0 0 10px #fff;
    }

    input, input[type="file"] {
      padding: 10px;
      font-size: 18px;
      border: none;
      border-radius: 6px;
      outline: none;
      width: 250px;
      margin-bottom: 15px;
    }

    button {
      padding: 10px 20px;
      font-size: 16px;
      background-color: #ffc107;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      margin-bottom: 15px;
    }

    .output-container {
      display: none;
      flex-direction: column;
      align-items: center;
      margin-top: 30px;
      position: relative;
    }

    .preview-img {
      max-width: 300px;
      max-height: 300px;
      margin-bottom: 15px;
      border-radius: 10px;
      box-shadow: 0 0 10px #fff;
    }

    .output-text {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      font-size: 32px;
      font-weight: bold;
      text-shadow: 2px 2px 8px #000;
      pointer-events: none;
      text-align: center;
      color: #fff;
    }

    .share-links {
      margin-top: 20px;
    }

    .share-links a {
      color: white;
      margin: 0 10px;
      text-decoration: none;
      font-weight: bold;
    }

    canvas {
      position: fixed;
      top: 0;
      left: 0;
      pointer-events: none;
      z-index: 999;
    }
  </style>
</head>
<body>

<canvas id="confetti"></canvas>

<div class="container" id="formContainer">
  <h2>Generate Eid Card</h2>
  <input type="text" id="nameInput" placeholder="Enter Your Name...">
  <br>
  <input type="file" id="imageInput" accept="image/*">
  <br>
  <button onclick="generateMessage()">Generate</button>
</div>

<div class="output-container" id="outputContainer">
  <img id="preview" class="preview-img" />
  <div class="output-text" id="outputText"></div>
  <div class="share-links">
    <p>🔗 Share this card:</p>
    <a href="#" id="facebookShare" target="_blank">Facebook</a>
    <a href="#" id="twitterShare" target="_blank">Twitter</a>
    <a href="#" id="whatsappShare" target="_blank">WhatsApp</a>
    <a href="#" id="copyLink" onclick="copyCurrentUrl()">📋 Copy Link</a>
    <p id="copyStatus" style="color: lightgreen; display: none;">Link copied!</p>
  </div>
</div>

<script>
  function sleep(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
  }

  async function typeText(text, element) {
    element.innerHTML = '';
    for (let i = 0; i < text.length; i++) {
      element.innerHTML += text.charAt(i);
      await sleep(100);
    }
  }

  function generateMessage() {
    const name = document.getElementById('nameInput').value.trim();
    const preview = document.getElementById('preview');
    const outputText = document.getElementById('outputText');
    const outputContainer = document.getElementById('outputContainer');
    const formContainer = document.getElementById('formContainer');

    if (name !== '' && preview.src) {
      formContainer.style.display = 'none';
      outputContainer.style.display = 'flex';
      const finalText = `EID MUBARAK, ${name}! 🎉`;
      typeText(finalText, outputText);
      speakText(finalText);
      startConfetti();

      const shareText = encodeURIComponent(finalText);
      const shareUrl = encodeURIComponent(window.location.href);
      document.getElementById('facebookShare').href = `https://www.facebook.com/sharer/sharer.php?u=${shareUrl}&quote=${shareText}`;
      document.getElementById('twitterShare').href = `https://twitter.com/intent/tweet?text=${shareText}&url=${shareUrl}`;
      document.getElementById('whatsappShare').href = `https://api.whatsapp.com/send?text=${shareText}%20${shareUrl}`;
    }
  }

  function speakText(text) {
    const speech = new SpeechSynthesisUtterance(text);
    speech.lang = 'en-US';
    speech.pitch = 1;
    speech.rate = 1;
    speech.volume = 1;
    window.speechSynthesis.speak(speech);
  }

  function copyCurrentUrl() {
    navigator.clipboard.writeText(window.location.href).then(() => {
      document.getElementById('copyStatus').style.display = 'block';
      setTimeout(() => {
        document.getElementById('copyStatus').style.display = 'none';
      }, 2000);
    });
  }

  document.getElementById('imageInput').addEventListener('change', function(event) {
    const file = event.target.files[0];
    const reader = new FileReader();
    reader.onload = function(e) {
      const preview = document.getElementById('preview');
      preview.src = e.target.result;
    }
    if (file) {
      reader.readAsDataURL(file);
    }
  });

  const canvas = document.getElementById('confetti');
  const ctx = canvas.getContext('2d');
  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;

  const confettiPieces = Array.from({ length: 200 }, () => ({
    x: Math.random() * canvas.width,
    y: Math.random() * canvas.height,
    radius: Math.random() * 6 + 2,
    color: `hsl(${Math.random() * 360}, 100%, 60%)`,
    speed: Math.random() * 5 + 1,
    angle: Math.random() * 2 * Math.PI
  }));

  function drawConfetti() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    for (let c of confettiPieces) {
      ctx.beginPath();
      ctx.arc(c.x, c.y, c.radius, 0, Math.PI * 2);
      ctx.fillStyle = c.color;
      ctx.fill();
      c.y += c.speed;
      c.x += Math.sin(c.angle) * 2;
      if (c.y > canvas.height) {
        c.y = -10;
        c.x = Math.random() * canvas.width;
      }
    }
  }

  function startConfetti() {
    setInterval(drawConfetti, 20);
  }

  window.addEventListener('resize', () => {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
  });
</script>

</body>
</html>
