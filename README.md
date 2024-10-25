- 👋 Hi, I’m @betoo15
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
betoo15/betoo15 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
<!DOCTYPE html>
<html lang="pt-BR">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mario Completo</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background-color: lightblue;
        }

        canvas {
            display: block;
            background-color: #87CEEB;
        }

        .score {
            position: absolute;
            top: 10px;
            left: 10px;
            font-size: 20px;
            font-family: Arial, sans-serif;
            color: black;
        }
    </style>
</head>

<body>
    <div class="score">Pontuação: 0</div>
    <canvas id="gameCanvas"></canvas>

    <script>
        // Configurações do Canvas
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        let score = 0; // Pontuação inicial
        let gameOver = false; // Controle para fim de jogo

        // Configurações do "Mario"
        const mario = {
            x: 50,
            y: canvas.height - 150,
            width: 50,
            height: 50,
            speed: 5,
            velocityY: 0,
            jumpForce: 25,
            gravity: 0,
            grounded: false
        };

        // Configurações das moedas
        const coins = [
            // { x: 300, y: canvas.height - 100, radius: 15, collected: false },
            // { x: 400, y: canvas.height - 200, radius: 15, collected: false },
            // { x: 600, y: canvas.height - 100, radius: 15, collected: false },
            // { x: 600, y: canvas.height - 200, radius: 15, collected: false }
        ];

        // Configuração das plataformas
        const platforms = [
            { x: 0, y: canvas.height - 50, width: canvas.width, height: 50 },
            { x: 250, y: canvas.height - 420, width: 150, height: 10 },
            { x: 550, y: canvas.height - 220, width: 150, height: 10 }
        ];

        // Configuração dos inimigos (obstáculos)
        const enemies = [
            // { x: 500, y: canvas.height - 400, width: 50, height: 50, speed: 2, direction: 1 },

        ];

        // Movimentos
        let keys = {
            ArrowRight: false,
            ArrowLeft: false,
            ArrowUp: false,
            ArrowDown: false,
            Space: false
        };

        // Controle do movimento e salto
        document.addEventListener('keydown', (e) => {
            if (e.code === 'ArrowRight') keys.ArrowRight = true;
            if (e.code === 'ArrowLeft') keys.ArrowLeft = true;
            if (e.code === 'ArrowUp') keys.ArrowUp = true;
            if (e.code === 'ArrowDown') keys.ArrowDown = true;
            if (e.code === 'Space' && mario.grounded) {
                mario.velocityY = -mario.jumpForce;
                mario.grounded = false;
            }

            console.log(e.code)
        });

        document.addEventListener('keyup', (e) => {
            if (e.code === 'ArrowRight') keys.ArrowRight = false;
            if (e.code === 'ArrowLeft') keys.ArrowLeft = false;
            if (e.code === 'ArrowUp') keys.ArrowUp = false;
            if (e.code === 'ArrowDown') keys.ArrowDown = false;
        });

        // Função para verificar colisão
        function checkCollision(a, b) {
            return a.x < b.x + b.width && a.x + a.width > b.x && a.y < b.y + b.height && a.y + a.height > b.y;
        }

        // Função para atualizar o movimento
        function update() {
            if (gameOver) return;

            // Movimento lateral
            if (keys.ArrowRight) mario.x += mario.speed;
            if (keys.ArrowLeft) mario.x -= mario.speed;

            if (keys.ArrowDown) mario.y += mario.speed;
            if (keys.ArrowUp) mario.y -= mario.speed;

            // Aplica gravidade
            mario.velocityY += mario.gravity;
            mario.y += mario.velocityY;

            // Mantém o Mario em cima das plataformas
            mario.grounded = false;
            for (let platform of platforms) {
                if (mario.y + mario.height >= platform.y && mario.y + mario.height <= platform.y + platform.height &&
                    mario.x + mario.width >= platform.x && mario.x <= platform.x + platform.width) {
                    mario.y = platform.y - mario.height;
                    mario.velocityY = 0;
                    mario.grounded = true;
                }
            }

            // Limite de tela para o Mario não sair
            if (mario.x < 0) mario.x = 0;
            if (mario.x + mario.width > canvas.width) mario.x = canvas.width - mario.width;

            // Coleta de moedas
            for (let index = 0; index < coins.length; index++) {
                var coin = coins[index]
                

                if (!coin.collected && checkCollision(mario, { x: coin.x - coin.radius, y: coin.y - coin.radius, width: coin.radius * 2, height: coin.radius * 2 })) {
                    coin.collected = true;
                    // delete coins[index]

                    coins.splice(index, 1);
                    break;

                    // 

                    score += 10; // Adiciona pontos
                    document.querySelector('.score').innerText = "Pontuação: " + score;
                }
            }

            // Movimento dos inimigos e colisão
            for (let enemy of enemies) {
                enemy.x += enemy.speed * (enemy.direction/ 10); // fazer se mover de acordo com a drieção 
                enemy.y += enemy.speed * (enemy.directionY/10); // fazer se mover de acordo com a drieção 


                // if (enemy.x <= 0 || enemy.x + enemy.width >= canvas.width) {
                //     enemy.direction *= -1; // Inverte a direção
                // }

                if (checkCollision(mario, enemy)) {
                    gameOver = true; // Se o Mario colidir com o inimigo, o jogo acaba
                    alert("Game Over! Sua pontuação: " + score);
                    window.location.reload();
                }
            }
        }


        // gerar inimigos  
        var count = 0
        function newEnemy() {
            // enemy.x += enemy.speed * enemy.direction;

            
            enemies.push({
                x: canvas.width - 1,
                y: Math.floor(Math.random() *  canvas.height),
                width: 50,
                height: 50,
                speed: count++ / 10 + 7,
                direction: Math.floor(Math.random() * -50),
                directionY: Math.floor(Math.random() * 100) - 50,
            })



            
        }


        function newCoin() {

            console.log(coins.length)
            if(coins.length >= 5) return;


            coins.push({
                x: Math.floor(Math.random() *  canvas.width),
                y: Math.floor(Math.random() *  canvas.height),
                radius: 15,
                collected: false 
            })



        }
        setInterval(newEnemy, 200 )
        setInterval(newCoin, 10)

        // Função para desenhar o jogo
        function draw() {
            if (gameOver) return;

            // Limpa o canvas
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Desenha o Mario
            ctx.fillStyle = 'red';
            ctx.fillRect(mario.x, mario.y, mario.width, mario.height);

            // Desenha as plataformas
            ctx.fillStyle = 'green';
            for (let platform of platforms) {
                ctx.fillRect(platform.x, platform.y, platform.width, platform.height);
            }

            // Desenha as moedas
            ctx.fillStyle = 'yellow';
            for (let coin of coins) {
                if (!coin.collected) {
                    ctx.beginPath();
                    ctx.arc(coin.x, coin.y, coin.radius, 0, Math.PI * 2);
                    ctx.fill();
                }
            }

            // Desenha os inimigos
            ctx.fillStyle = 'purple';
            for (let enemy of enemies) {
                ctx.fillRect(enemy.x, enemy.y, enemy.width, enemy.height);
            }
        }

        // Função principal do jogo
        function gameLoop() {
            update();
            draw();
            requestAnimationFrame(gameLoop);
        }

        // Inicia o loop do jogo
        gameLoop();
    </script>
</body>

</html>
