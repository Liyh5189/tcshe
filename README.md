# tcshe
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>滑动控制贪吃蛇</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            touch-action: none;
            -webkit-tap-highlight-color: transparent;
            user-select: none;
        }
        
        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
            color: #fff;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            padding: 20px;
            overflow-x: hidden;
        }
        
        .container {
            display: flex;
            flex-direction: column;
            align-items: center;
            max-width: 1000px;
            width: 100%;
        }
        
        header {
            text-align: center;
            margin-bottom: 20px;
            width: 100%;
        }
        
        h1 {
            font-size: clamp(32px, 7vw, 48px);
            color: #4dff91;
            text-shadow: 0 0 10px rgba(77, 255, 145, 0.5);
            margin-bottom: 10px;
            letter-spacing: 2px;
        }
        
        .subtitle {
            font-size: clamp(16px, 3vw, 20px);
            color: #a0a0ff;
            margin-bottom: 5px;
        }
        
        .game-container {
            position: relative;
            width: 100%;
            max-width: 600px;
            margin: 0 auto;
            border-radius: 15px;
            overflow: hidden;
            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.5);
            background-color: #0f3460;
            border: 3px solid #4dff91;
        }
        
        canvas {
            display: block;
            width: 100%;
            height: auto;
            background-color: #0d1b2a;
        }
        
        .stats {
            display: flex;
            justify-content: space-between;
            width: 100%;
            max-width: 600px;
            background-color: rgba(15, 52, 96, 0.8);
            color: white;
            padding: 15px 20px;
            border-radius: 10px;
            margin-bottom: 15px;
            font-size: clamp(16px, 3vw, 20px);
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
            border: 2px solid #4dff91;
        }
        
        .stat-item {
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        
        .stat-value {
            font-size: clamp(24px, 5vw, 32px);
            font-weight: bold;
            color: #4dff91;
            margin-top: 5px;
        }
        
        .controls {
            display: flex;
            flex-direction: column;
            align-items: center;
            width: 100%;
            max-width: 600px;
            margin-top: 20px;
        }
        
        .keyboard-controls {
            color: #a0a0ff;
            margin-bottom: 15px;
            text-align: center;
            font-size: clamp(14px, 2.5vw, 16px);
        }
        
        .touch-hint {
            display: none;
            color: #a0a0ff;
            text-align: center;
            font-size: clamp(14px, 2.5vw, 16px);
            margin-top: 15px;
            padding: 10px;
            background-color: rgba(15, 52, 96, 0.5);
            border-radius: 10px;
            width: 100%;
        }
        
        .dpad {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            grid-template-rows: repeat(3, 1fr);
            gap: 8px;
            margin: 0 auto;
        }
        
        .dpad-btn {
            background: linear-gradient(145deg, #1a1a2e, #0f3460);
            border: none;
            color: #4dff91;
            font-size: 24px;
            width: 70px;
            height: 70px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 15px;
            box-shadow: 5px 5px 10px rgba(0, 0, 0, 0.5), -5px -5px 10px rgba(255, 255, 255, 0.05);
            cursor: pointer;
            transition: all 0.1s;
        }
        
        .dpad-btn:active {
            box-shadow: inset 5px 5px 10px rgba(0, 0, 0, 0.5), inset -5px -5px 10px rgba(255, 255, 255, 0.05);
            transform: scale(0.95);
        }
        
        .dpad-up {
            grid-column: 2;
            grid-row: 1;
        }
        
        .dpad-left {
            grid-column: 1;
            grid-row: 2;
        }
        
        .dpad-right {
            grid-column: 3;
            grid-row: 2;
        }
        
        .dpad-down {
            grid-column: 2;
            grid-row: 3;
        }
        
        .instructions {
            background-color: rgba(15, 52, 96, 0.8);
            border-radius: 15px;
            padding: 20px;
            margin-top: 25px;
            width: 100%;
            max-width: 600px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
            border: 2px solid #4dff91;
        }
        
        .instructions h3 {
            color: #4dff91;
            margin-bottom: 15px;
            font-size: clamp(18px, 4vw, 22px);
            text-align: center;
        }
        
        .instructions p {
            margin-bottom: 10px;
            line-height: 1.6;
            font-size: clamp(14px, 2.5vw, 16px);
        }
        
        .instructions ul {
            margin-left: 20px;
            margin-bottom: 15px;
        }
        
        .instructions li {
            margin-bottom: 8px;
        }
        
        .game-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            background-color: rgba(13, 27, 42, 0.95);
            color: white;
            z-index: 10;
            padding: 20px;
            text-align: center;
        }
        
        .game-overlay h2 {
            color: #4dff91;
            font-size: clamp(32px, 7vw, 48px);
            margin-bottom: 20px;
            text-shadow: 0 0 15px rgba(77, 255, 145, 0.7);
        }
        
        .game-overlay p {
            font-size: clamp(18px, 4vw, 24px);
            margin-bottom: 25px;
            max-width: 500px;
        }
        
        .btn {
            background: linear-gradient(145deg, #1a1a2e, #0f3460);
            color: #4dff91;
            border: 2px solid #4dff91;
            padding: 15px 35px;
            font-size: clamp(18px, 4vw, 22px);
            border-radius: 50px;
            cursor: pointer;
            font-weight: bold;
            margin-top: 10px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
            transition: all 0.2s;
        }
        
        .btn:hover, .btn:active {
            background: linear-gradient(145deg, #4dff91, #1a1a2e);
            color: #0d1b2a;
            transform: scale(1.05);
        }
        
        .hidden {
            display: none !important;
        }
        
        .footer {
            margin-top: 25px;
            color: #a0a0ff;
            text-align: center;
            font-size: 14px;
            padding: 10px;
        }
        
        .snake-color {
            color: #4dff91;
            font-weight: bold;
        }
        
        .food-color {
            color: #ff6b6b;
            font-weight: bold;
        }
        
        .mobile-only {
            display: none;
        }
        
        @media (max-width: 768px) {
            .touch-hint {
                display: block;
            }
            
            .keyboard-controls {
                display: none;
            }
            
            .mobile-only {
                display: inline;
            }
            
            .dpad-btn {
                width: 60px;
                height: 60px;
                font-size: 22px;
            }
            
            .game-container {
                border-width: 2px;
            }
        }
        
        @media (max-width: 480px) {
            .dpad-btn {
                width: 55px;
                height: 55px;
                font-size: 20px;
            }
            
            body {
                padding: 10px;
            }
        }
        
        .swipe-indicator {
            position: absolute;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 5;
            display: none;
        }
        
        .swipe-arrow {
            position: absolute;
            color: rgba(77, 255, 145, 0.7);
            font-size: 50px;
            text-shadow: 0 0 10px rgba(77, 255, 145, 0.5);
            opacity: 0;
            transition: opacity 0.3s;
        }
        
        .difficulty-selector {
            display: flex;
            justify-content: center;
            gap: 15px;
            margin-top: 15px;
            margin-bottom: 20px;
            width: 100%;
            max-width: 600px;
        }
        
        .difficulty-btn {
            background: rgba(15, 52, 96, 0.8);
            color: #a0a0ff;
            border: 2px solid #4dff91;
            padding: 10px 20px;
            border-radius: 50px;
            cursor: pointer;
            font-weight: bold;
            transition: all 0.2s;
            flex: 1;
            max-width: 180px;
            text-align: center;
        }
        
        .difficulty-btn.active {
            background: linear-gradient(145deg, #4dff91, #1a1a2e);
            color: #0d1b2a;
        }
        
        .difficulty-btn:hover, .difficulty-btn:active {
            background: rgba(77, 255, 145, 0.3);
            color: #4dff91;
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>滑动贪吃蛇</h1>
            <div class="subtitle">滑动屏幕控制蛇的移动，收集食物，避免撞墙或撞到自己！</div>
        </header>
        
        <div class="difficulty-selector">
            <div class="difficulty-btn active" id="easyBtn">简单</div>
            <div class="difficulty-btn" id="mediumBtn">中等</div>
            <div class="difficulty-btn" id="hardBtn">困难</div>
        </div>
        
        <div class="stats">
            <div class="stat-item">
                <div>分数</div>
                <div class="stat-value" id="score">0</div>
            </div>
            <div class="stat-item">
                <div>长度</div>
                <div class="stat-value" id="length">3</div>
            </div>
            <div class="stat-item">
                <div>最高分</div>
                <div class="stat-value" id="highScore">0</div>
            </div>
        </div>
        
        <div class="game-container">
            <canvas id="gameCanvas"></canvas>
            <div class="swipe-indicator" id="swipeIndicator">
                <div class="swipe-arrow" id="upArrow">↑</div>
                <div class="swipe-arrow" id="downArrow">↓</div>
                <div class="swipe-arrow" id="leftArrow">←</div>
                <div class="swipe-arrow" id="rightArrow">→</div>
            </div>
            
            <div id="startScreen" class="game-overlay">
                <h2>滑动贪吃蛇</h2>
                <p><span class="mobile-only">在屏幕上滑动来控制方向<br>或使用方向按钮</span><span class="desktop-only">使用键盘方向键控制</span></p>
                <p>收集<span class="food-color">红色食物</span>让<span class="snake-color">蛇</span>变长</p>
                <p>避免撞墙或撞到自己</p>
                <button id="startBtn" class="btn">开始游戏</button>
            </div>
            
            <div id="gameOverScreen" class="game-overlay hidden">
                <h2>游戏结束</h2>
                <p id="gameOverMessage">你的分数: <span id="finalScore">0</span></p>
                <p id="newHighScore" class="hidden" style="color:#4dff91">新纪录!</p>
                <button id="restartBtn" class="btn">重新开始</button>
            </div>
        </div>
        
        <div class="controls">
            <div class="keyboard-controls">
                键盘控制: 方向键 ← ↑ → ↓
            </div>
            
            <div class="touch-hint">
                在屏幕上滑动控制方向，或使用方向按钮
            </div>
            
            <div class="dpad">
                <button class="dpad-btn dpad-up" id="upBtn">↑</button>
                <button class="dpad-btn dpad-left" id="leftBtn">←</button>
                <button class="dpad-btn dpad-right" id="rightBtn">→</button>
                <button class="dpad-btn dpad-down" id="downBtn">↓</button>
            </div>
        </div>
        
        <div class="instructions">
            <h3>游戏说明</h3>
            <ul>
                <li><span class="snake-color">绿色蛇</span>会自动向前移动</li>
                <li><span class="mobile-only"><strong>手机控制：</strong>在屏幕上滑动改变方向，或使用方向按钮</span><span class="desktop-only"><strong>电脑控制：</strong>使用键盘方向键</span></li>
                <li>收集<span class="food-color">红色食物</span>增加蛇的长度和分数</li>
                <li>每吃一个食物得10分，长度增加1</li>
                <li>撞到墙壁或自己的身体游戏结束</li>
                <li>随着分数增加，游戏速度会逐渐加快</li>
            </ul>
            <p>提示：预判蛇的移动路径，提前规划方向！</p>
        </div>
        
        <div class="footer">
            <p>滑动控制贪吃蛇游戏 | 专为移动设备优化 | 使用HTML5 Canvas开发</p>
        </div>
    </div>

    <script>
        // 游戏主对象
        const game = {
            canvas: null,
            ctx: null,
            gridSize: 20,
            width: 600,
            height: 600,
            scale: 1,
            score: 0,
            highScore: 0,
            gameRunning: false,
            gameOver: false,
            lastTime: 0,
            speed: 10, // 初始速度 (帧/秒)
            difficulty: 'easy',
            snake: null,
            food: null,
            direction: null,
            nextDirection: null,
            touchStartX: 0,
            touchStartY: 0,
            minSwipeDistance: 30, // 最小滑动距离
            
            init() {
                this.canvas = document.getElementById('gameCanvas');
                this.ctx = this.canvas.getContext('2d');
                
                // 设置Canvas尺寸
                this.resizeCanvas();
                window.addEventListener('resize', () => this.resizeCanvas());
                
                // 从本地存储获取最高分
                this.highScore = localStorage.getItem('snakeHighScore') || 0;
                document.getElementById('highScore').textContent = this.highScore;
                
                // 初始化游戏
                this.resetGame();
                
                // 设置游戏循环
                this.gameLoop();
                
                // 初始化事件监听器
                this.setupEventListeners();
                
                // 初始化难度选择
                this.setupDifficulty();
            },
            
            resizeCanvas() {
                const container = document.querySelector('.game-container');
                const containerWidth = container.clientWidth;
                
                // 保持正方形画布
                const size = Math.min(containerWidth, 600);
                this.canvas.width = size;
                this.canvas.height = size;
                
                // 调整网格大小以适应不同屏幕
                this.gridSize = Math.max(15, Math.floor(size / 30));
                
                // 重新绘制游戏
                if (this.gameRunning || this.gameOver) {
                    this.draw();
                }
            },
            
            setupEventListeners() {
                // 键盘控制
                window.addEventListener('keydown', (e) => {
                    if (!this.gameRunning) return;
                    
                    switch(e.key) {
                        case 'ArrowUp':
                            if (this.direction !== 'down') this.nextDirection = 'up';
                            break;
                        case 'ArrowDown':
                            if (this.direction !== 'up') this.nextDirection = 'down';
                            break;
                        case 'ArrowLeft':
                            if (this.direction !== 'right') this.nextDirection = 'left';
                            break;
                        case 'ArrowRight':
                            if (this.direction !== 'left') this.nextDirection = 'right';
                            break;
                    }
                    
                    // 防止方向键滚动页面
                    if (['ArrowUp', 'ArrowDown', 'ArrowLeft', 'ArrowRight'].includes(e.key)) {
                        e.preventDefault();
                    }
                });
                
                // 触摸/滑动控制
                this.canvas.addEventListener('touchstart', (e) => {
                    if (!this.gameRunning) return;
                    
                    const touch = e.touches[0];
                    this.touchStartX = touch.clientX;
                    this.touchStartY = touch.clientY;
                    
                    // 显示滑动指示器
                    this.showSwipeIndicator(touch.clientX, touch.clientY);
                    
                    e.preventDefault();
                }, { passive: false });
                
                this.canvas.addEventListener('touchmove', (e) => {
                    e.preventDefault(); // 防止页面滚动
                }, { passive: false });
                
                this.canvas.addEventListener('touchend', (e) => {
                    if (!this.gameRunning || !this.touchStartX) return;
                    
                    const touch = e.changedTouches[0];
                    const deltaX = touch.clientX - this.touchStartX;
                    const deltaY = touch.clientY - this.touchStartY;
                    
                    // 隐藏滑动指示器
                    this.hideSwipeIndicator();
                    
                    // 检查是否是有效的滑动
                    if (Math.abs(deltaX) < this.minSwipeDistance && Math.abs(deltaY) < this.minSwipeDistance) {
                        return; // 不是有效的滑动
                    }
                    
                    // 确定滑动方向
                    if (Math.abs(deltaX) > Math.abs(deltaY)) {
                        // 水平滑动
                        if (deltaX > 0 && this.direction !== 'left') {
                            this.nextDirection = 'right';
                            this.showDirectionArrow('right');
                        } else if (deltaX < 0 && this.direction !== 'right') {
                            this.nextDirection = 'left';
                            this.showDirectionArrow('left');
                        }
                    } else {
                        // 垂直滑动
                        if (deltaY > 0 && this.direction !== 'up') {
                            this.nextDirection = 'down';
                            this.showDirectionArrow('down');
                        } else if (deltaY < 0 && this.direction !== 'down') {
                            this.nextDirection = 'up';
                            this.showDirectionArrow('up');
                        }
                    }
                    
                    // 重置触摸起点
                    this.touchStartX = 0;
                    this.touchStartY = 0;
                    
                    e.preventDefault();
                }, { passive: false });
                
                // 方向按钮控制
                document.getElementById('upBtn').addEventListener('click', () => {
                    if (this.gameRunning && this.direction !== 'down') this.nextDirection = 'up';
                });
                document.getElementById('downBtn').addEventListener('click', () => {
                    if (this.gameRunning && this.direction !== 'up') this.nextDirection = 'down';
                });
                document.getElementById('leftBtn').addEventListener('click', () => {
                    if (this.gameRunning && this.direction !== 'right') this.nextDirection = 'left';
                });
                document.getElementById('rightBtn').addEventListener('click', () => {
                    if (this.gameRunning && this.direction !== 'left') this.nextDirection = 'right';
                });
                
                // 游戏控制按钮
                document.getElementById('startBtn').addEventListener('click', () => this.startGame());
                document.getElementById('restartBtn').addEventListener('click', () => this.restartGame());
            },
            
            setupDifficulty() {
                const easyBtn = document.getElementById('easyBtn');
                const mediumBtn = document.getElementById('mediumBtn');
                const hardBtn = document.getElementById('hardBtn');
                
                easyBtn.addEventListener('click', () => {
                    this.difficulty = 'easy';
                    this.setDifficultyActive(easyBtn, mediumBtn, hardBtn);
                });
                
                mediumBtn.addEventListener('click', () => {
                    this.difficulty = 'medium';
                    this.setDifficultyActive(mediumBtn, easyBtn, hardBtn);
                });
                
                hardBtn.addEventListener('click', () => {
                    this.difficulty = 'hard';
                    this.setDifficultyActive(hardBtn, easyBtn, mediumBtn);
                });
            },
            
            setDifficultyActive(activeBtn, ...otherBtns) {
                activeBtn.classList.add('active');
                otherBtns.forEach(btn => btn.classList.remove('active'));
            },
            
            showSwipeIndicator(x, y) {
                const indicator = document.getElementById('swipeIndicator');
                indicator.style.display = 'block';
                
                // 设置箭头位置
                document.getElementById('upArrow').style.top = '10%';
                document.getElementById('upArrow').style.left = '50%';
                document.getElementById('downArrow').style.bottom = '10%';
                document.getElementById('downArrow').style.left = '50%';
                document.getElementById('leftArrow').style.top = '50%';
                document.getElementById('leftArrow').style.left = '10%';
                document.getElementById('rightArrow').style.top = '50%';
                document.getElementById('rightArrow').style.right = '10%';
            },
            
            hideSwipeIndicator() {
                const indicator = document.getElementById('swipeIndicator');
                indicator.style.display = 'none';
                
                // 隐藏所有箭头
                const arrows = document.querySelectorAll('.swipe-arrow');
                arrows.forEach(arrow => arrow.style.opacity = '0');
            },
            
            showDirectionArrow(direction) {
                const arrow = document.getElementById(`${direction}Arrow`);
                if (arrow) {
                    arrow.style.opacity = '1';
                    setTimeout(() => {
                        arrow.style.opacity = '0';
                    }, 300);
                }
            },
            
            resetGame() {
                // 重置游戏状态
                this.score = 0;
                this.gameRunning = false;
                this.gameOver = false;
                
                // 根据难度设置初始速度
                switch(this.difficulty) {
                    case 'easy':
                        this.speed = 8;
                        break;
                    case 'medium':
                        this.speed = 12;
                        break;
                    case 'hard':
                        this.speed = 16;
                        break;
                }
                
                // 初始化蛇
                const startX = Math.floor(this.canvas.width / this.gridSize / 2) * this.gridSize;
                const startY = Math.floor(this.canvas.height / this.gridSize / 2) * this.gridSize;
                
                this.snake = [
                    {x: startX, y: startY},
                    {x: startX - this.gridSize, y: startY},
                    {x: startX - this.gridSize * 2, y: startY}
                ];
                
                // 初始方向向右
                this.direction = 'right';
                this.nextDirection = 'right';
                
                // 生成第一个食物
                this.generateFood();
                
                // 更新显示
                this.updateDisplay();
                
                // 隐藏游戏结束界面
                document.getElementById('gameOverScreen').classList.add('hidden');
                document.getElementById('newHighScore').classList.add('hidden');
            },
            
            startGame() {
                this.resetGame();
                this.gameRunning = true;
                document.getElementById('startScreen').classList.add('hidden');
            },
            
            restartGame() {
                this.resetGame();
                this.gameRunning = true;
                document.getElementById('gameOverScreen').classList.add('hidden');
            },
            
            updateDisplay() {
                document.getElementById('score').textContent = this.score;
                document.getElementById('length').textContent = this.snake.length;
                document.getElementById('highScore').textContent = this.highScore;
            },
            
            gameLoop(timestamp) {
                // 计算时间增量
                const deltaTime = timestamp - this.lastTime || 0;
                this.lastTime = timestamp;
                
                // 控制游戏速度
                const interval = 1000 / this.speed;
                
                // 如果游戏正在运行，更新游戏状态
                if (this.gameRunning && deltaTime > interval) {
                    this.update();
                    this.lastTime = timestamp - (deltaTime % interval);
                }
                
                // 绘制游戏
                this.draw();
                
                // 继续游戏循环
                requestAnimationFrame((ts) => this.gameLoop(ts));
            },
            
            update() {
                // 更新方向
                if (this.nextDirection) {
                    this.direction = this.nextDirection;
                    this.nextDirection = null;
                }
                
                // 计算新的蛇头位置
                const head = {...this.snake[0]};
                
                switch(this.direction) {
                    case 'up':
                        head.y -= this.gridSize;
                        break;
                    case 'down':
                        head.y += this.gridSize;
                        break;
                    case 'left':
                        head.x -= this.gridSize;
                        break;
                    case 'right':
                        head.x += this.gridSize;
                        break;
                }
                
                // 检查是否撞墙
                if (head.x < 0 || head.x >= this.canvas.width || 
                    head.y < 0 || head.y >= this.canvas.height) {
                    this.endGame();
                    return;
                }
                
                // 检查是否撞到自己
                for (let segment of this.snake) {
                    if (head.x === segment.x && head.y === segment.y) {
                        this.endGame();
                        return;
                    }
                }
                
                // 移动蛇
                this.snake.unshift(head);
                
                // 检查是否吃到食物
                if (head.x === this.food.x && head.y === this.food.y) {
                    // 增加分数
                    this.score += 10;
                    
                    // 随着分数增加速度
                    if (this.score % 50 === 0) {
                        this.speed += 2;
                    }
                    
                    // 生成新食物
                    this.generateFood();
                    
                    // 更新显示
                    this.updateDisplay();
                } else {
                    // 如果没有吃到食物，移除尾部
                    this.snake.pop();
                }
            },
            
            draw() {
                // 清除画布
                this.ctx.fillStyle = '#0d1b2a';
                this.ctx.fillRect(0, 0, this.canvas.width, this.canvas.height);
                
                // 绘制网格
                this.drawGrid();
                
                // 绘制蛇
                this.drawSnake();
                
                // 绘制食物
                this.drawFood();
                
                // 绘制游戏边界
                this.drawBorder();
            },
            
            drawGrid() {
                this.ctx.strokeStyle = 'rgba(77, 255, 145, 0.1)';
                this.ctx.lineWidth = 1;
                
                // 垂直线
                for (let x = 0; x <= this.canvas.width; x += this.gridSize) {
                    this.ctx.beginPath();
                    this.ctx.moveTo(x, 0);
                    this.ctx.lineTo(x, this.canvas.height);
                    this.ctx.stroke();
                }
                
                // 水平线
                for (let y = 0; y <= this.canvas.height; y += this.gridSize) {
                    this.ctx.beginPath();
                    this.ctx.moveTo(0, y);
                    this.ctx.lineTo(this.canvas.width, y);
                    this.ctx.stroke();
                }
            },
            
            drawSnake() {
                // 绘制蛇身
                for (let i = 0; i < this.snake.length; i++) {
                    const segment = this.snake[i];
                    
                    // 蛇头使用不同颜色
                    if (i === 0) {
                        this.ctx.fillStyle = '#4dff91';
                    } else {
                        // 蛇身使用渐变色
                        const alpha = 1 - (i / this.snake.length) * 0.7;
                        this.ctx.fillStyle = `rgba(77, 255, 145, ${alpha})`;
                    }
                    
                    // 绘制圆角矩形
                    this.drawRoundedRect(
                        segment.x, 
                        segment.y, 
                        this.gridSize, 
                        this.gridSize, 
                        this.gridSize / 4
                    );
                    
                    // 绘制蛇眼睛（只在头部）
                    if (i === 0) {
                        this.ctx.fillStyle = '#0d1b2a';
                        const eyeSize = this.gridSize / 5;
                        
                        // 根据方向确定眼睛位置
                        let leftEyeX, leftEyeY, rightEyeX, rightEyeY;
                        
                        switch(this.direction) {
                            case 'right':
                                leftEyeX = segment.x + this.gridSize - eyeSize * 2;
                                leftEyeY = segment.y + eyeSize * 2;
                                rightEyeX = segment.x + this.gridSize - eyeSize * 2;
                                rightEyeY = segment.y + this.gridSize - eyeSize * 3;
                                break;
                            case 'left':
                                leftEyeX = segment.x + eyeSize;
                                leftEyeY = segment.y + eyeSize * 2;
                                rightEyeX = segment.x + eyeSize;
                                rightEyeY = segment.y + this.gridSize - eyeSize * 3;
                                break;
                            case 'up':
                                leftEyeX = segment.x + eyeSize * 2;
                                leftEyeY = segment.y + eyeSize;
                                rightEyeX = segment.x + this.gridSize - eyeSize * 3;
                                rightEyeY = segment.y + eyeSize;
                                break;
                            case 'down':
                                leftEyeX = segment.x + eyeSize * 2;
                                leftEyeY = segment.y + this.gridSize - eyeSize * 2;
                                rightEyeX = segment.x + this.gridSize - eyeSize * 3;
                                rightEyeY = segment.y + this.gridSize - eyeSize * 2;
                                break;
                        }
                        
                        this.ctx.beginPath();
                        this.ctx.arc(leftEyeX, leftEyeY, eyeSize, 0, Math.PI * 2);
                        this.ctx.fill();
                        
                        this.ctx.beginPath();
                        this.ctx.arc(rightEyeX, rightEyeY, eyeSize, 0, Math.PI * 2);
                        this.ctx.fill();
                    }
                }
            },
            
            drawRoundedRect(x, y, width, height, radius) {
                this.ctx.beginPath();
                this.ctx.moveTo(x + radius, y);
                this.ctx.lineTo(x + width - radius, y);
                this.ctx.quadraticCurveTo(x + width, y, x + width, y + radius);
                this.ctx.lineTo(x + width, y + height - radius);
                this.ctx.quadraticCurveTo(x + width, y + height, x + width - radius, y + height);
                this.ctx.lineTo(x + radius, y + height);
                this.ctx.quadraticCurveTo(x, y + height, x, y + height - radius);
                this.ctx.lineTo(x, y + radius);
                this.ctx.quadraticCurveTo(x, y, x + radius, y);
                this.ctx.closePath();
                this.ctx.fill();
            },
            
            drawFood() {
                // 绘制食物（苹果形状）
                this.ctx.fillStyle = '#ff6b6b';
                
                // 绘制主体（圆形）
                this.ctx.beginPath();
                this.ctx.arc(
                    this.food.x + this.gridSize/2, 
                    this.food.y + this.gridSize/2, 
                    this.gridSize/2 - 1, 
                    0, 
                    Math.PI * 2
                );
                this.ctx.fill();
                
                // 绘制高光
                this.ctx.fillStyle = '#ff9e9e';
                this.ctx.beginPath();
                this.ctx.arc(
                    this.food.x + this.gridSize/3, 
                    this.food.y + this.gridSize/3, 
                    this.gridSize/6, 
                    0, 
                    Math.PI * 2
                );
                this.ctx.fill();
                
                // 绘制茎
                this.ctx.fillStyle = '#8b4513';
                this.ctx.fillRect(
                    this.food.x + this.gridSize/2 - 1, 
                    this.food.y + 2, 
                    2, 
                    this.gridSize/3
                );
                
                // 绘制叶子
                this.ctx.fillStyle = '#32cd32';
                this.ctx.beginPath();
                this.ctx.ellipse(
                    this.food.x + this.gridSize/2 + 3, 
                    this.food.y + this.gridSize/4, 
                    this.gridSize/4, 
                    this.gridSize/6, 
                    Math.PI/4, 
                    0, 
                    Math.PI * 2
                );
                this.ctx.fill();
            },
            
            drawBorder() {
                this.ctx.strokeStyle = '#4dff91';
                this.ctx.lineWidth = 3;
                this.ctx.strokeRect(0, 0, this.canvas.width, this.canvas.height);
            },
            
            generateFood() {
                // 生成不在蛇身上的随机位置
                let foodX, foodY;
                let foodOnSnake;
                
                do {
                    foodOnSnake = false;
                    foodX = Math.floor(Math.random() * (this.canvas.width / this.gridSize)) * this.gridSize;
                    foodY = Math.floor(Math.random() * (this.canvas.height / this.gridSize)) * this.gridSize;
                    
                    // 检查食物是否在蛇身上
                    for (let segment of this.snake) {
                        if (foodX === segment.x && foodY === segment.y) {
                            foodOnSnake = true;
                            break;
                        }
                    }
                } while (foodOnSnake);
                
                this.food = {x: foodX, y: foodY};
            },
            
            endGame() {
                this.gameRunning = false;
                this.gameOver = true;
                
                // 更新最高分
                if (this.score > this.highScore) {
                    this.highScore = this.score;
                    localStorage.setItem('snakeHighScore', this.highScore);
                    document.getElementById('newHighScore').classList.remove('hidden');
                }
                
                // 显示游戏结束界面
                document.getElementById('finalScore').textContent = this.score;
                document.getElementById('gameOverMessage').textContent = `游戏结束! 得分: ${this.score}`;
                document.getElementById('gameOverScreen').classList.remove('hidden');
            }
        };
        
        // 初始化游戏
        window.addEventListener('load', () => {
            game.init();
        });
    </script>
</body>
</html>
