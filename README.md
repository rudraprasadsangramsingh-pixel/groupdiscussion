<Group Discussion>
<html>
<head>
  <title>Group Discussion Hub</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 20px; }
    #discussion { border: 1px solid #ccc; padding: 10px; height: 300px; overflow-y: scroll; }
    .message { margin: 5px 0; }
    .bot { color: blue; }
    .user { color: green; }
    .judge { color: purple; font-weight: bold; }
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

    // Random bot names
    const botNames = ["Aria", "Leo", "Maya", "Raj", "Nina"];
    const shuffled = botNames.sort(() => 0.5 - Math.random());

    // Simulated bot responses
    const bots = [
      `${shuffled[0]} (AI): CSR is evolving into strategic philanthropy.`,
      `${shuffled[1]} (Google): Reports show CSR spending in India crossed ₹25,000 crore.`,
      `${shuffled[2]} (AI Counterpoint): Many firms treat CSR as compliance, not impact.`
    ];

    // Add bot messages with speech
    function addBotMessages() {
      bots.forEach((msg, i) => {
        setTimeout(() => {
          const div = document.createElement("div");
          div.className = "message bot";
          div.textContent = msg;
          discussion.appendChild(div);

          // Speak the bot message
          const utterance = new SpeechSynthesisUtterance(msg);
          speechSynthesis.speak(utterance);

          // After last bot, judge evaluates
          if (i === bots.length - 1) {
            setTimeout(judgeDiscussion, 3000);
          }
        }, (i+1) * 3000);
      });
    }

    // Judge bot evaluation
    function judgeDiscussion() {
      const score = Math.floor(Math.random() * 5) + 6; // random 6–10
      const msg = `${shuffled[3]} (Judge): Based on clarity and impact, I give this round ${score}/10.`;
      const div = document.createElement("div");
      div.className = "message judge";
      div.textContent = msg;
      discussion.appendChild(div);

      // Speak judge message
      const utterance = new SpeechSynthesisUtterance(msg);
      speechSynthesis.speak(utterance);
    }

    // Speech recognition setup
    const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
    const recognition = new SpeechRecognition();

    recognition.onresult = (event) => {
      const userMsg = event.results[0][0].transcript;
      const div = document.createElement("div");
      div.className = "message user";
      div.textContent = `${shuffled[4]} (You): ${userMsg}`;
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

