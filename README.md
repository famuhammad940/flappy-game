<!DOCTYPE html>
<html>
<head>
    <title>Flappy Game</title>
    <style>
        body { margin: 0; background: #4ec0ca; display: flex; justify-content: center; align-items: center; height: 100vh; }
        canvas { border: 2px solid black; }
    </style>
</head>
<body>
<canvas id="game" width="400" height="600"></canvas>
<script>
    const canvas = document.getElementById('game');
    const ctx = canvas.getContext('2d');
    let bird = { x: 50, y: 300, r: 12, v: 0 };
    let pipes = [];
    let score = 0;
    let running = true;
    const gravity = 0.25;
    const jumpPower = -5;

    function addPipe() {
        let gap = 140;
        let top = Math.random() * (canvas.height - gap - 50) + 50;
        pipes.push({ x: canvas.width, top: top, bottom: top + gap, counted: false });
    }

    function update() {
        if (!running) return;
        bird.v += gravity;
        bird.y += bird.v;
        if (bird.y + bird.r > canvas.height || bird.y - bird.r < 0) running = false;
        for (let i = 0; i < pipes.length; i++) {
            pipes[i].x -= 2;
            if (!pipes[i].counted && pipes[i].x + 60 < bird.x) {
                pipes[i].counted = true;
                score++;
            }
            if (bird.x + bird.r > pipes[i].x && bird.x - bird.r < pipes[i].x + 60) {
                if (bird.y - bird.r < pipes[i].top || bird.y + bird.r > pipes[i].bottom) {
                    running = false;
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
        ctx.arc(bird.x, bird.y, bird.r, 0, Math.PI * 2);
        ctx.fill();
        ctx.fillStyle = 'black';
        ctx.font = '30px Arial';
        ctx.fillText(score, canvas.width / 2, 50);
    }

    function loop() {
        update();
        draw();
        requestAnimationFrame(loop);
    }

    setInterval(() => { if (running) addPipe(); }, 2000);
    canvas.addEventListener('click', () => { if (running) bird.v = jumpPower; });
    loop();
</script>
</body>
</html>
