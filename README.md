
        var gk_isXlsx = false;
        var gk_xlsxFileLookup = {};
        var gk_fileData = {};
        function filledCell(cell) {
          return cell !== '' && cell != null;
        }
        function loadFileData(filename) {
        if (gk_isXlsx && gk_xlsxFileLookup[filename]) {
            try {
                var workbook = XLSX.read(gk_fileData[filename], { type: 'base64' });
                var firstSheetName = workbook.SheetNames[0];
                var worksheet = workbook.Sheets[firstSheetName];

                // Convert sheet to JSON to filter blank rows
                var jsonData = XLSX.utils.sheet_to_json(worksheet, { header: 1, blankrows: false, defval: '' });
                // Filter out blank rows (rows where all cells are empty, null, or undefined)
                var filteredData = jsonData.filter(row => row.some(filledCell));

                // Heuristic to find the header row by ignoring rows with fewer filled cells than the next row
                var headerRowIndex = filteredData.findIndex((row, index) =>
                  row.filter(filledCell).length >= filteredData[index + 1]?.filter(filledCell).length
                );
                // Fallback
                if (headerRowIndex === -1 || headerRowIndex > 25) {
                  headerRowIndex = 0;
                }

                // Convert filtered JSON back to CSV
                var csv = XLSX.utils.aoa_to_sheet(filteredData.slice(headerRowIndex)); // Create a new sheet from filtered array of arrays
                csv = XLSX.utils.sheet_to_csv(csv, { header: 1 });
                return csv;
            } catch (e) {
                console.error(e);
                return "";
            }
        }
        return gk_fileData[filename] || "";
        }
        </script><!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chúc Mừng Sinh Nhật Lung Linh! 🎉</title>
    <style>
        body {
            margin: 0;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            background: linear-gradient(135deg, #2a1a3f, #6b5b95); /* Nền tím đậm trời đêm */
            font-family: 'Arial', sans-serif;
            overflow: hidden;
            touch-action: none;
        }
        canvas#starCanvas {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 1;
        }
        canvas#textCanvas {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 2;
        }
        .name-corner {
            position: fixed;
            top: 10px;
            left: 10px;
            font-size: 1em;
            font-weight: bold;
            color: #ff3366;
            animation: argbLed 5s linear infinite;
            z-index: 3;
        }
        @keyframes argbLed {
            0% { color: #ff3366; }
            20% { color: #ff6b6b; }
            40% { color: #ffd1dc; }
            60% { color: #a1c4fd; }
            80% { color: #d4a5ff; }
            100% { color: #ff3366; }
        }
    </style>
</head>
<body>
    <canvas id="starCanvas"></canvas>
    <canvas id="textCanvas"></canvas>
    <div class="name-corner">Hải Thành (Minazure)</div>

    <script>
        const starCanvas = document.getElementById('starCanvas');
        const starCtx = starCanvas.getContext('2d');
        starCanvas.width = window.innerWidth;
        starCanvas.height = window.innerHeight;

        const textCanvas = document.getElementById('textCanvas');
        const textCtx = textCanvas.getContext('2d');
        textCanvas.width = window.innerWidth;
        textCanvas.height = window.innerHeight;

        let stars = [];
        let particles = [];
        const wishes = ["Happy Birthday!", "Chúc Mừng Sinh Nhật!"]; // Thay lời chúc ở đây
        const colors = ['#ffd1dc', '#ffeb99']; // Màu cho từng lời chúc
        let currentWishIndex = 0;
        let phase = 'form';
        let phaseTime = 0;
        const phaseDuration = 8000; // 8 giây để chậm

        function createStars() {
            stars = [];
            for (let i = 0; i < 70; i++) {
                stars.push({
                    x: Math.random() * starCanvas.width,
                    y: Math.random() * starCanvas.height,
                    size: Math.random() * 5 + 2,
                    vx: Math.random() * 2 - 1,
                    vy: Math.random() * 2 - 1,
                    color: '#ffffff', // Ngôi sao trắng sáng
                    dragging: false
                });
            }
        }

        function updateStars() {
            starCtx.clearRect(0, 0, starCanvas.width, starCanvas.height);
            starCtx.shadowBlur = 10; // Glow cho ngôi sao
            starCtx.shadowColor = '#ffffff';
            stars.forEach(s => {
                if (!s.dragging) {
                    s.x += s.vx;
                    s.y += s.vy;
                    if (s.x < 0 || s.x > starCanvas.width) s.vx = -s.vx;
                    if (s.y < 0 || s.y > starCanvas.height) s.vy = -s.vy;
                }
                drawStar(s.x, s.y, s.size, s.color);
            });
            starCtx.shadowBlur = 0; // Tắt glow sau khi vẽ
        }

        function drawStar(x, y, size, color) {
            starCtx.beginPath();
            for (let i = 0; i < 5; i++) {
                starCtx.lineTo(x + size * Math.cos(2 * Math.PI * i / 5 - Math.PI / 2), y + size * Math.sin(2 * Math.PI * i / 5 - Math.PI / 2));
                starCtx.lineTo(x + size / 2 * Math.cos(2 * Math.PI * (i + 0.5) / 5 - Math.PI / 2), y + size / 2 * Math.sin(2 * Math.PI * (i + 0.5) / 5 - Math.PI / 2));
            }
            starCtx.closePath();
            starCtx.fillStyle = color;
            starCtx.fill();
        }

        function createParticles() {
            particles = [];
            const currentText = wishes[currentWishIndex];
            const currentColor = colors[currentWishIndex];
            textCtx.font = 'bold 60px Arial';
            textCtx.textAlign = 'center';
            textCtx.textBaseline = 'middle';
            textCtx.fillStyle = currentColor;
            textCtx.shadowBlur = 15; // Lấp lánh cho chữ
            textCtx.shadowColor = currentColor;
            const textMetrics = textCtx.measureText(currentText);
            const textWidth = textMetrics.width;
            const textHeight = 60;
            const x = textCanvas.width / 2;
            const y = textCanvas.height / 2;

            textCtx.clearRect(0, 0, textCanvas.width, textCanvas.height);
            textCtx.fillText(currentText, x, y);
            const imageData = textCtx.getImageData(x - textWidth / 2, y - textHeight / 2, textWidth, textHeight);
            textCtx.clearRect(0, 0, textCanvas.width, textCanvas.height);

            for (let i = 0; i < imageData.width; i += 4) {
                for (let j = 0; j < imageData.height; j += 4) {
                    if (imageData.data[(j * imageData.width + i) * 4 + 3] > 128) {
                        const particle = {
                            homeX: i - textWidth / 2 + x,
                            homeY: j - textHeight / 2 + y,
                            x: Math.random() * textCanvas.width,
                            y: Math.random() * textCanvas.height,
                            size: Math.random() * 3 + 2,
                            color: currentColor,
                            vx: Math.random() * 2 - 1,
                            vy: Math.random() * 2 - 1,
                            angle: Math.random() * Math.PI * 2,
                            radius: Math.random() * 100 + 50
                        };
                        particles.push(particle);
                    }
                }
            }
            textCtx.shadowBlur = 0; // Tắt lấp lánh
        }

        function updateParticles() {
            textCtx.clearRect(0, 0, textCanvas.width, textCanvas.height);
            const currentColor = colors[currentWishIndex];
            particles.forEach(p => {
                if (phase === 'form') {
                    p.angle += 0.05;
                    const targetX = p.homeX + Math.cos(p.angle) * p.radius;
                    const targetY = p.homeY + Math.sin(p.angle) * p.radius;
                    p.x = lerp(p.x, p.homeX, 0.015);
                    p.y = lerp(p.y, p.homeY, 0.015);
                    p.radius = lerp(p.radius, 0, 0.015);
                    const gradient = textCtx.createRadialGradient(p.x, p.y, 0, p.x, p.y, p.size);
                    gradient.addColorStop(0, currentColor);
                    gradient.addColorStop(1, '#d4a5ff');
                    p.color = gradient;
                } else {
                    p.x += p.vx;
                    p.y += p.vy;
                    if (p.x < 0 || p.x > textCanvas.width) p.vx = -p.vx;
                    if (p.y < 0 || p.y > textCanvas.height) p.vy = -p.vy;
                    p.color = currentColor;
                }
                textCtx.beginPath();
                textCtx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
                textCtx.fillStyle = p.color;
                textCtx.fill();
            });

            phaseTime += 16;
            if (phaseTime > phaseDuration) {
                phase = phase === 'form' ? 'scatter' : 'form';
                phaseTime = 0;
                if (phase === 'form') {
                    currentWishIndex = (currentWishIndex + 1) % wishes.length;
                    createParticles();
                }
            }
        }

        function lerp(start, end, t) {
            return start + (end - start) * t;
        }

        starCanvas.addEventListener('touchstart', (e) => {
            const touch = e.touches[0];
            stars.forEach(s => {
                if (Math.hypot(touch.clientX - s.x, touch.clientY - s.y) < 20) {
                    s.dragging = true;
                    s.dragX = touch.clientX - s.x;
                    s.dragY = touch.clientY - s.y;
                }
            });
        });

        starCanvas.addEventListener('touchmove', (e) => {
            const touch = e.touches[0];
            stars.forEach(s => {
                if (s.dragging) {
                    s.x = touch.clientX - s.dragX;
                    s.y = touch.clientY - s.dragY;
                }
            });
        });

        starCanvas.addEventListener('touchend', () => {
            stars.forEach(s => s.dragging = false);
        });

        function animate() {
            updateStars();
            updateParticles();
            requestAnimationFrame(animate);
        }

        createStars();
        createParticles();
        animate();
    </script>
</body>
</html>
