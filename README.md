<!DOCTYPE html>
<html>
<head>
  <title>Collecting Coins Game</title>
  <style>
    /* CSS for game interface */
    /* Add your custom styles here */
    body {
      font-family: Arial, sans-serif;
      background-color: #000000;
      margin: 0;
      overflow: hidden;
    }
    
    #gameContainer {
      position: relative;
      width: 100%;
      height: calc(100vh - 60px);
      display: flex;
      align-items: center;
      justify-content: center;
    }
    
    #character {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      width: 50px;
      height: 50px;
      background-color: #FFFFFF;
      border-radius: 50%;
    }
    
    .coin {
      position: absolute;
      width: 30px;
      height: 30px;
      background-color: #FFD700;
      border-radius: 50%;
    }
    
    #scoreFrame {
      position: fixed;
      top: 10px;
      left: 10px;
      padding: 10px;
      background-color: #FFFFFF;
      color: #000000;
      font-size: 18px;
      border-radius: 5px;
    }

    #medalFrame {
      position: fixed;
      top: 10px;
      right: 10px;
      padding: 10px;
      background-color: #FFFFFF;
      color: #000000;
      font-size: 18px;
      border-radius: 5px;
    }

    .medal {
      display: inline-block;
      width: 30px;
      height: 30px;
      background-color: #FFD700;
      margin-right: 5px;
      border-radius: 50%;
    }

    #globalScoreFrame {
      position: fixed;
      bottom: 10px;
      left: 10px;
      padding: 10px;
      background-color: #FFFFFF;
      color: #000000;
      font-size: 18px;
      border-radius: 5px;
    }
  </style>
</head>
<body>
  <div id="gameContainer">
    <div id="character"></div>
  </div>
  
  <div id="scoreFrame">Score: <span id="score">0</span></div>
  <div id="medalFrame">Medals: <span id="medals"></span></div>
  <div id="globalScoreFrame">Global Score: <span id="globalScore">Loading...</span></div>

  <script src="https://www.gstatic.com/firebasejs/9.0.0/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.0.0/firebase-database.js"></script>
  <script>
    // Client-side JavaScript code
    // Add your custom code here to handle game functionality

    var gameContainer = document.getElementById("gameContainer");
    var character = document.getElementById("character");
    var score = 0;
    var medals = [];
    var globalScoreRef;

    var firebaseConfig = {
      // Your Firebase configuration goes here
      // Replace with your own Firebase project's config
    };

    // Initialize Firebase
    firebase.initializeApp(firebaseConfig);

    // Get a reference to the global score in the database
    globalScoreRef = firebase.database().ref("globalScore");

    // Listen for changes in the global score and update the display
    globalScoreRef.on("value", function(snapshot) {
      var globalScore = snapshot.val();
      document.getElementById("globalScore").innerText = globalScore || 0;
    });

    // Function to collect a coin and update the score
    function collectCoin(coin) {
      coin.remove();
      score += 10;
      updateScore();

      if (score === 100) {
        awardMedal("#FFD700");
      } else if (score === 1000) {
        awardMedal("#C0C0C0");
      } else if (score === 5000) {
        awardMedal("#FFA500");
      }

      // Update the global score in the database
      globalScoreRef.transaction(function(currentScore) {
        return (currentScore || 0) + 10;
      });
    }

    // Function to move the character to the coins
    function moveCharacterToCoin(coin) {
      var characterPosition = {
        x: character.offsetLeft + character.offsetWidth / 2,
        y: character.offsetTop + character.offsetHeight / 2
      };

      var coinPosition = {
        x: coin.offsetLeft + coin.offsetWidth / 2,
        y: coin.offsetTop + coin.offsetHeight / 2
      };

      var dx = coinPosition.x - characterPosition.x;
      var dy = coinPosition.y - characterPosition.y;
      var distance = Math.sqrt(dx * dx + dy * dy);

      var speed = 3;
      var duration = distance / speed;

      var startTime = null;
      function step(timestamp) {
        if (!startTime) startTime = timestamp;
        var progress = timestamp - startTime;

        var newX = characterPosition.x + (dx / duration) * progress;
        var newY = characterPosition.y + (dy / duration) * progress;

        character.style.left = newX - character.offsetWidth / 2 + "px";
        character.style.top = newY - character.offsetHeight / 2 + "px";

        if (progress < duration) {
          window.requestAnimationFrame(step);
        } else {
          collectCoin(coin);
        }
      }

      window.requestAnimationFrame(step);
    }

    // Function to update the score display
    function updateScore() {
      document.getElementById("score").innerText = score;
    }

    // Function to award a medal
    function awardMedal(type) {
      var medal = document.createElement("div");
      medal.className = "medal";
      medal.style.backgroundColor = type;
      document.getElementById("medals").appendChild(medal);
      medals.push(type);
    }
  </script>
</body>
</html>
