<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Number Guessing Game</title>
  <style>
    body {
      font-family: "Poppins", sans-serif;
      background-color: #2e2e2e;
      color: #f5c542;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
    }

    .game-container {
      background: #1f1f1f;
      padding: 40px;
      border-radius: 20px;
      box-shadow: 0px 0px 20px rgba(245, 197, 66, 0.6);
      text-align: center;
      width: 400px;
      animation: fadeIn 1s ease-in-out;
    }

    h1 {
      font-size: 2em;
      margin-bottom: 10px;
      color: #ffd700;
    }

    p {
      margin-bottom: 20px;
      font-size: 1.1em;
    }

    input {
      padding: 12px;
      width: 200px;
      border: 2px solid #ffd700;
      border-radius: 10px;
      outline: none;
      background: #333;
      color: #fff;
      font-size: 1em;
      text-align: center;
      transition: 0.3s;
    }

    input:focus {
      border-color: #f5c542;
      box-shadow: 0 0 10px #f5c542;
    }

    button {
      margin-top: 15px;
      padding: 12px 25px;
      background: linear-gradient(135deg, #ffd700, #f5a623);
      border: none;
      border-radius: 10px;
      cursor: pointer;
      font-weight: bold;
      font-size: 1em;
      color: #1f1f1f;
      transition: transform 0.2s ease, box-shadow 0.2s ease;
    }

    button:hover {
      transform: translateY(-3px);
      box-shadow: 0px 5px 15px rgba(245, 197, 66, 0.8);
    }

    #result {
      margin-top: 25px;
      font-size: 1.2em;
      font-weight: bold;
      min-height: 30px;
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(-20px); }
      to { opacity: 1; transform: translateY(0); }
    }
  </style>
</head>
<body>
  <div class="game-container">
    <h1>🌌By Empire X Terra🌒<h1>
    <h1>🎯 Guess the Number</h1>
    <p>Pick a number between <b>1 and 804</b></p>
    <input type="number" id="guess" placeholder="Enter your guess">
    <br>
    <button onclick="checkGuess()">Submit</button>
    <button onclick="restartGame()">Restart</button>

    <p id="result"></p>
  </div>

  <script>
    let randomNumber = Math.floor(Math.random() * 804) + 1;

    // Peaceful Music Function
    function playMusic() {
      const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
      const notes = [261.63, 329.63, 392.00, 523.25]; // C, E, G, C

      let startTime = audioCtx.currentTime;
      notes.forEach((freq, index) => {
        const osc = audioCtx.createOscillator();
        const gainNode = audioCtx.createGain();

        osc.type = "sine";
        osc.frequency.setValueAtTime(freq, startTime + index * 1.5);

        gainNode.gain.setValueAtTime(0.001, startTime + index * 1.5);
        gainNode.gain.exponentialRampToValueAtTime(0.3, startTime + index * 1.5 + 0.1);
        gainNode.gain.exponentialRampToValueAtTime(0.001, startTime + index * 1.5 + 1.5);

        osc.connect(gainNode).connect(audioCtx.destination);
        osc.start(startTime + index * 1.5);
        osc.stop(startTime + index * 1.5 + 1.5);
      });
    }

    function checkGuess() {
      let userGuess = document.getElementById("guess").value;
      let result = document.getElementById("result");

      if (!userGuess) {
        result.textContent = "⚠️ Please enter a number!";
        result.style.color = "orange";
        return;
      }

      userGuess = Number(userGuess);

      if (userGuess === randomNumber) {
        result.textContent = "🎉 Correct! You guessed it!";
        result.style.color = "lime";
        playMusic(); // ✅ Play music when correct
      } else if (userGuess < randomNumber) {
        result.textContent = "📉 Too low! Try again.";
        result.style.color = "#ff6961";
      } else {
        result.textContent = "📈 Too high! Try again.";
        result.style.color = "#ff6961";
      }
    }

    function restartGame() {
      randomNumber = Math.floor(Math.random() * 100) + 1;
      document.getElementById("result").textContent = "";
      document.getElementById("guess").value = "";
    }
  </script>
</body>
</html>
