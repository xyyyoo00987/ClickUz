<!DOCTYPE html>
<html lang="uz">
<head>
  <meta charset="UTF-8">
  <title>AdeeM Robot – So‘zlar fayldan</title>
  <style>
    body {
      margin: 0;
      background: black;
      color: #9effff;
      font-family: monospace;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
    }

    .face {
      position: relative;
      width: 240px;
      height: 240px;
      background: black;
      margin-bottom: 30px;
    }

    .eye {
      width: 50px;
      height: 70px;
      background: #9effff;
      border-radius: 50% 50% 50% 50% / 60% 60% 40% 40%;
      position: absolute;
      top: 50px;
      box-shadow: 0 0 12px #9effffcc;
    }

    .eye.left { left: 30px; }
    .eye.right { right: 30px; }

    .mouth {
      width: 60px;
      height: 30px;
      background: #9effff;
      border-radius: 0 0 30px 30px;
      position: absolute;
      bottom: 60px;
      left: 50%;
      transform: translateX(-50%);
      box-shadow: 0 0 15px #9effff88;
    }

    .text-output {
      font-size: 22px;
      color: #9effff;
      text-shadow: 0 0 6px #9effff99;
      min-height: 30px;
      text-align: center;
    }

    button {
      margin-top: 10px;
      background: #111;
      color: #9effff;
      border: 1px solid #9effff;
      padding: 10px 20px;
      font-size: 16px;
      cursor: pointer;
      border-radius: 8px;
    }

    button:hover {
      background: #222;
    }
  </style>
</head>
<body>

  <div class="face">
    <div class="eye left"></div>
    <div class="eye right"></div>
    <div class="mouth"></div>
  </div>

  <div class="text-output" id="output">🎙 Gapiring: Masalan “Salom”</div>
  <button onclick="startListening()">🎧 Boshlash</button>

  <script>
    const output = document.getElementById('output');
    let responses = {};

    // So‘zlar JSON fayldan yuklanadi
    fetch("responses.json")
      .then(res => res.json())
      .then(data => {
        responses = data;
      })
      .catch(err => {
        output.textContent = "❌ So‘z faylini o‘qib bo‘lmadi: " + err;
      });

    document.body.addEventListener('click', () => {
      startListening();
    });

    function startListening() {
      const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;

      if (!SpeechRecognition) {
        output.textContent = "❌ Brauzeringiz gapni eshita olmaydi";
        return;
      }

      const recognition = new SpeechRecognition();
      recognition.lang = "uz-UZ";
      recognition.interimResults = false;
      recognition.maxAlternatives = 1;

      recognition.start();
      output.textContent = "🎤 Eshitilyapti...";

      recognition.onresult = function(event) {
        const transcript = event.results[0][0].transcript.toLowerCase().trim();
        output.textContent = transcript;

        const key = Object.keys(responses).find(k => transcript.includes(k));
        const reply = key ? responses[key] : "Kechirasiz, tushunmadim. Qaytaring iltimos.";

        speakUzbekText(reply);
      };

      recognition.onerror = function(event) {
        output.textContent = "❌ Xatolik: " + event.error;
      };
    }

    function speakUzbekText(text) {
      const synth = window.speechSynthesis;
      const utterance = new SpeechSynthesisUtterance(text);
      utterance.lang = "ru-RU";
      utterance.rate = 0.85;
      utterance.pitch = 1.3;
      synth.speak(utterance);
    }
  </script>

</body>
</html>
