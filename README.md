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
    .judge { color: purple; font-weight: bold; }
    #liveText { margin-top: 10px; font-style: italic; color: gray; }
    #timer { font-size: 18px; font-weight: bold; margin-bottom: 10px; }
    button, select, input { margin-top: 10px; padding: 10px; }
  </style>
</head>
<body>
  <h1>Group Discussion Hub</h1>

  <!-- Topic Selector -->
  <label for="topic">Choose Topic:</label>
  <select id="topic">
    <option value="CSR">CSR in India</option>
    <option value="Inflation">Inflation & Policy</option>
    <option value="Startups">Startup Ecosystem</option>
  </select>

  <!-- Timer Input -->
  <label for="time">Set GD Time (seconds):</label>
  <input type="number" id="time" value="30">

  <button id="startBtn">Start Discussion</button>
  <div id="timer">Time Left: 0s</div>

  <div id="discussion"></div>
  <button id="speakBtn">🎤 Speak</button>
  <div id="liveText">Press Speak and start talking...</div>

  <script>
    const discussion = document.getElementById("discussion");
    const speakBtn = document.getElementById("speakBtn");
    const startBtn = document.getElementById("startBtn");
    const liveText = document.getElementById("liveText");
    const timerDisplay = document.getElementById("timer");
    const topicSelect = document.getElementById("topic");
    const timeInput = document.getElementById("time");

    let countdown;
    let timeLeft;
    let userPoints = [];

    // Random bot names
    const botNames = ["Aria", "Leo", "Maya", "Raj", "Nina"];
    const shuffled = botNames.sort(() => 0.5 - Math.random());

    // Bot personalities
    const roles = {
      supporter: shuffled[0],
      factBot: shuffled[1],
      counter: shuffled[2],
      judge: shuffled[3],
      user: shuffled[4]
    };

    // Simulated bot responses by topic
    const topicResponses = {
      "CSR": [
        "CSR is evolving into strategic philanthropy.",
        "CSR spending in India crossed ₹25,000 crore.",
        "Many firms treat CSR as compliance, not impact."
      ],
      "Inflation": [
        "Inflation impacts purchasing power directly.",
        "India’s CPI inflation averaged 5.4% last year.",
        "Policy tightening can slow growth while controlling inflation."
      ],
      "Startups": [
        "India’s startup ecosystem is the third largest globally.",
        "Over 100 unicorns have emerged in India.",
        "Many startups struggle with scalability and funding."
      ]
    };

    // Add bot messages continuously
    function addBotMessages(topic) {
      const bots = topicResponses[topic];
      let i = 0;
      const interval = setInterval(() => {
        if (timeLeft <= 0) {
          clearInterval(interval);
          return;
        }
        const role = i % 3 === 0 ? roles.supporter : (i % 3 === 1 ? roles.factBot : roles.counter);
        const msg = `${role}: ${bots[i % bots.length]}`;
        const div = document.createElement("div");
        div.className = "message bot";
        div.textContent = msg;
        discussion.appendChild(div);

        // Speak the bot message
        const utterance = new SpeechSynthesisUtterance(msg);
        speechSynthesis.speak(utterance);

        i++;
      }, 4000);
    }

    // Judge bot evaluation
    function judgeDiscussion() {
      const score = Math.floor(Math.random() * 5) + 6; // random 6–10
      const msg = `${roles.judge} (Judge): Based on clarity and impact, I give YOU ${score}/10. Your points: ${userPoints.join("; ")}`;
      const div = document.createElement("div");
      div.className = "message judge";
      div.textContent = msg;
      discussion.appendChild(div);

      const utterance = new SpeechSynthesisUtterance(msg);
      speechSynthesis.speak(utterance);
    }

    // Timer function
    function startTimer(duration, topic) {
      timeLeft = duration;
      countdown = setInterval(() => {
        timeLeft--;
        timerDisplay.textContent = "Time Left: " + timeLeft + "s";
        if (timeLeft <= 0) {
          clearInterval(countdown);
          timerDisplay.textContent = "Time’s up!";
          judgeDiscussion();
        }
      }, 1000);

      addBotMessages(topic);
    }

    // Speech recognition setup
    const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
    const recognition = new SpeechRecognition();
    recognition.interimResults = true;

    recognition.onresult = (event) => {
      let transcript = "";
      for (let i = event.resultIndex; i < event.results.length; i++) {
        transcript += event.results[i][0].transcript;
      }
      liveText.textContent = "You (speaking): " + transcript;

      if (event.results[0].isFinal) {
        const div = document.createElement("div");
        div.className = "message user";
        div.textContent = `${roles.user} (You): ${transcript}`;
        discussion.appendChild(div);
        userPoints.push(transcript);
        liveText.textContent = "Press Speak and start talking...";
      }
    };

    speakBtn.onclick = () => {
      recognition.start();
      liveText.textContent = "Listening...";
    };

    startBtn.onclick = () => {
      discussion.innerHTML = ""; // clear previous
      userPoints = [];
      const topic = topicSelect.value;
      const duration = parseInt(timeInput.value);
      timerDisplay.textContent = "Time Left: " + duration + "s";
      startTimer(duration, topic);
    };
  </script>
</body>
</html>

