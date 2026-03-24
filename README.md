<!DOCTYPE html>
<html>
<head>
    <title>Flappy Bird</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            background: #4ec0ca;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }
        canvas {
            border: 2px solid black;
        }
    </style>
</head>
<body>
    <canvas id="canvas" width="400" height="600"></canvas>
    <script>
        const canvas = document.getElementById('canvas');
        const ctx = canvas.getContext('2d');
        
        let bird = { x: 50, y: 300, radius: 12, velocity: 0, gravity: 0.25, jump: -5 };
        let pipes = [];
        let score = 0;
        let gameRunning = true;
        
        function addPipe() {
            let gap = 140;
            let top = Math.random() * (canvas.height - gap - 50) + 50;
            pipes.push({ x: canvas.width, top: top, bottom: top + gap, counted: false });
        }
        
        function update() {
            if (!gameRunning) return;
            bird.velocity += bird.gravity;
            bird.y += bird.velocity;
            if (bird.y + bird.radius > canvas.height || bird.y - bird.radius < 0) gameRunning = false;
            
            for (let i = 0; i < pipes.length; i++) {
                pipes[i].x -= 2;
                if (!pipes[i].counted && pipes[i].x + 60 < bird.x) {
                    pipes[i].counted = true;
                    score++;
                }
                if (bird.x + bird.radius > pipes[i].x && bird.x - bird.radius < pipes[i].x + 60) {
                    if (bird.y - bird.radius < pipes[i].top || bird.y + bird.radius > pipes[i].bottom) {
                        gameRunning = false;
                    }
                }
            }
            pipes = pipes.filter(p => p.x + 60 > 0);
        }
        
        function draw() {
            ctx.fillStyle = '#4ec0ca';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            ctx.fillStyle = '#2ecc71';
            for (let p of pipes) {
                ctx.fillRect(p.x, 0, 60, p.top);
                ctx.fillRect(p.x, p.bottom, 60, canvas.height - p.bottom);
            }
            ctx.fillStyle = '#f1c40f';
            ctx.beginPath();
            ctx.arc(bird.x, bird.y, bird.radius, 0, Math.PI * 2);
            ctx.fill();
            ctx.fillStyle = 'black';
            ctx.font = '30px Arial';
            ctx.fillText(score, canvas.width/2, 50);
        }
        
        function loop() {
            update();
            draw();
            requestAnimationFrame(loop);
        }
        
        setInterval(() => { if (gameRunning) addPipe(); }, 2000);
        canvas.addEventListener('click', () => { if (gameRunning) bird.velocity = bird.jump; });
        loop();
    </script>
</body>
</html>
