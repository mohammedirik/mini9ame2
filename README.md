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

  <script src="https://www.gstatic.com/firebasejs/9.5.0/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.5.0/firebase-database.js"></script>
  <script>
    // Your web app's Firebase configuration
    const firebaseConfig = {
      apiKey: "AIzaSyCE04GkMZHC6CW_bxGPmw9ct3ASUJvFYxg",
      authDomain: "my-project-1684241537544.firebaseapp.com",
      projectId: "my-project-1684241537544",
      storageBucket: "my-project-1684241537544.appspot.com",
      messagingSenderId: "836051800622",
      appId: "1:836051800622:web:00426d50818f04c3ef45bf",
      measurementId: "G-697PNJFGF0"
    };

    // Initialize Firebase
    firebase.initializeApp(firebaseConfig);

    // Get a reference to the global score in the database
    const globalScoreRef = firebase.database().ref("globalScore");

    // Listen for changes in the global score and update the display
    globalScoreRef.on("value", (snapshot) => {
      const globalScore = snapshot.val();
      document.getElementById("globalScore").innerText = globalScore || 0;
    });

    // Client-side JavaScript code
    // Add your custom code here to handle game functionality

    const gameContainer = document.getElementById("gameContainer");
    const character = document.getElementById("character");
    let score = 0;
    const medals = [];

    function deployCoin() {
      const coin = document.createElement("div");
      coin.className = "coin";

      // Randomly position the coin within the game container
      const coinX = Math.random() * (gameContainer.offsetWidth - coin.offsetWidth);
      const coinY = Math.random() * (gameContainer.offsetHeight - coin.offsetHeight);
      coin.style.left = coinX + "px";
      coin.style.top = coinY + "px";

      // Attach a click event listener to collect the coin when clicked
      coin.addEventListener("click", () => {
        moveCharacterToCoin(coin);
      });

      // Append the coin to the game container
      gameContainer.appendChild(coin);
    }

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
      globalScoreRef.transaction((currentScore) => (currentScore || 0) + 10);
      deployCoin();
    }

    // Function to move the character to the coins
    function moveCharacterToCoin(coin) {
      const characterPosition = {
        x: character.offsetLeft + character.offsetWidth / 2,
        y: character.offsetTop + character.offsetHeight / 2
      };

      const coinPosition = {
        x: coin.offsetLeft + coin.offsetWidth / 2,
        y: coin.offsetTop + coin.offsetHeight / 2
      };

      const dx = coinPosition.x - characterPosition.x;
      const dy = coinPosition.y - characterPosition.y;
      const distance = Math.sqrt(dx * dx + dy * dy);

      const speed = 3;
      const duration = distance / speed;

      let startTime = null;
      function step(timestamp) {
        if (!startTime) startTime = timestamp;
        const progress = timestamp - startTime;

        const newX = characterPosition.x + (dx / duration) * progress;
        const newY = characterPosition.y + (dy / duration) * progress;

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
      const medal = document.createElement("div");
      medal.className = "medal";
      medal.style.backgroundColor = type;
      document.getElementById("medals").appendChild(medal);
      medals.push(type);
    }

    // Call the deployCoin function to start deploying coins
    deployCoin();
  </script>
</body>
</html>
