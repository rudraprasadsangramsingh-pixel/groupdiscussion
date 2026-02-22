<!DOCTYPE html>
<html>
<head>
  <title>Group Discussion Hub</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 20px; }
    #discussion { border: 1px solid #ccc; padding: 10px; height: 300px; overflow-y: scroll; }
    .message { margin: 5px 0; }
    .bot { color: blue; }
    .user { color: green; }
    button { margin-top: 10px; padding: 10px; }
  </style>
</head>
<body>
  <h1>Group Discussion Hub</h1>
  <div id="discussion"></div>
  <button id="speakBtn">🎤 Speak</button>

  <script>
    const discussion = document.getElementById("discussion");
    const speakBtn = document.getElementById("speakBtn");

    // Simulated bot responses
    const bots = [
      "AI Bot: CSR is evolving into strategic philanthropy.",
      "Google Bot: According to recent reports, CSR spending in India crossed ₹25,000 crore.",
      "AI Bot 2: A counterpoint is that many firms treat CSR as compliance, not impact."
    ];

    // Add bot messages with delay
    function addBotMessages() {
      bots.forEach((msg, i) => {
        setTimeout(() => {
          const div = document.createElement("div");
          div.className = "message bot";
          div.textContent = msg;
          discussion.appendChild(div);
        }, (i+1) * 2000);
      });
    }

    // Speech recognition setup
    const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
    const recognition = new SpeechRecognition();

    recognition.onresult = (event) => {
      const userMsg = event.results[0][0].transcript;
      const div = document.createElement("div");
      div.className = "message user";
      div.textContent = "You: " + userMsg;
      discussion.appendChild(div);
    };

    speakBtn.onclick = () => {
      recognition.start();
    };

    // Start bots automatically
    addBotMessages();
  </script>
</body>
</html>
