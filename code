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
  </style>
</head>
<body>
  <div id="gameContainer">
    <div id="character"></div>
  </div>
  
  <div id="scoreFrame">Score: <span id="score">0</span></div>
  <div id="medalFrame">Medals: <span id="medals"></span></div>

  <script>
    // Client-side JavaScript code
    // Add your custom code here to handle game functionality

    var gameContainer = document.getElementById("gameContainer");
    var character = document.getElementById("character");
    var score = 0;
    var medals = [];

    gameContainer.addEventListener("click", collectCoin);

    function collectCoin(event) {
      var coin = document.createElement("div");
      coin.className = "coin";
      coin.style.top = event.clientY - 15 + "px";
      coin.style.left = event.clientX - 15 + "px";
      gameContainer.appendChild(coin);

      var characterPosition = {
        x: character.offsetLeft + character.offsetWidth / 2,
        y: character.offsetTop + character.offsetHeight / 2
      };

      moveCharacterToCoin(characterPosition, coin);

      score++;
      updateScore();

      if (score === 100) {
        awardMedal("gold");
      } else if (score === 1000) {
        awardMedal("silver");
      } else if (score === 5000) {
        awardMedal("bronze");
      }
    }

    function moveCharacterToCoin(characterPosition, coin) {
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
          gameContainer.removeChild(coin);
        }
      }

      window.requestAnimationFrame(step);
    }

    function updateScore() {
      document.getElementById("score").innerText = score;
    }

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
