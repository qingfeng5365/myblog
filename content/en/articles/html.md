+++
title = 'Html Test'
date = 2024-09-26T04:49:57-04:00
description = ""
tags = ["html"]
draft = true
+++

## html01

```html
<!DOCTYPE html>
<html lang="zh">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>俄罗斯方块</title>
    <style>
      canvas {
        border: 1px solid black;
        display: block;
        margin: auto;
      }
    </style>
  </head>
  <body>
    <canvas id="tetris" width="300" height="600"></canvas>
    <script>
      const canvas = document.getElementById("tetris");
      const context = canvas.getContext("2d");
      const ROWS = 20;
      const COLS = 10;
      const BLOCK_SIZE = 30;

      // 创建一个空的游戏区域
      let board = Array.from({ length: ROWS }, () => Array(COLS).fill(0));

      const shapes = [
        [[1, 1, 1, 1]], // I
        [
          [1, 1, 1],
          [0, 1, 0],
        ], // T
        [
          [1, 1, 0],
          [0, 1, 1],
        ], // S
        [
          [0, 1, 1],
          [1, 1, 0],
        ], // Z
        [
          [1, 1],
          [1, 1],
        ], // O
        [
          [1, 0, 0],
          [1, 1, 1],
        ], // L
        [
          [0, 0, 1],
          [1, 1, 1],
        ], // J
      ];

      let currentShape;
      let currentPosition;

      function randomShape() {
        const index = Math.floor(Math.random() * shapes.length);
        currentShape = shapes[index];
        currentPosition = { x: 4, y: 0 };
      }

      function draw() {
        context.clearRect(0, 0, canvas.width, canvas.height);
        board.forEach((row, y) => {
          row.forEach((value, x) => {
            if (value) {
              context.fillStyle = "blue";
              context.fillRect(
                x * BLOCK_SIZE,
                y * BLOCK_SIZE,
                BLOCK_SIZE,
                BLOCK_SIZE
              );
            }
          });
        });
        drawShape();
      }

      function drawShape() {
        currentShape.forEach((row, y) => {
          row.forEach((value, x) => {
            if (value) {
              context.fillStyle = "red";
              context.fillRect(
                (currentPosition.x + x) * BLOCK_SIZE,
                (currentPosition.y + y) * BLOCK_SIZE,
                BLOCK_SIZE,
                BLOCK_SIZE
              );
            }
          });
        });
      }

      function collision(offsetX, offsetY) {
        for (let y = 0; y < currentShape.length; y++) {
          for (let x = 0; x < currentShape[y].length; x++) {
            if (currentShape[y][x]) {
              const newX = currentPosition.x + x + offsetX;
              const newY = currentPosition.y + y + offsetY;
              if (
                newX < 0 ||
                newX >= COLS ||
                newY >= ROWS ||
                (newY >= 0 && board[newY][newX])
              ) {
                return true;
              }
            }
          }
        }
        return false;
      }

      function merge() {
        currentShape.forEach((row, y) => {
          row.forEach((value, x) => {
            if (value && currentPosition.y + y >= 0) {
              board[currentPosition.y + y][currentPosition.x + x] = 1;
            }
          });
        });
      }

      function removeLines() {
        board = board.reduce((acc, row) => {
          if (row.every((value) => value)) {
            acc.unshift(Array(COLS).fill(0)); // 添加新行
          } else {
            acc.push(row);
          }
          return acc;
        }, []);
      }

      function moveDown() {
        if (!collision(0, 1)) {
          currentPosition.y++;
        } else {
          merge();
          removeLines();
          randomShape();
        }
      }

      function rotate() {
        const rotatedShape = currentShape[0].map((_, index) =>
          currentShape.map((row) => row[index]).reverse()
        );
        if (!collision(0, 0)) {
          currentShape = rotatedShape;
        }
      }

      function moveLeft() {
        if (!collision(-1, 0)) {
          currentPosition.x--;
        }
      }

      function moveRight() {
        if (!collision(1, 0)) {
          currentPosition.x++;
        }
      }

      document.addEventListener("keydown", (event) => {
        if (event.key === "ArrowLeft") moveLeft();
        else if (event.key === "ArrowRight") moveRight();
        else if (event.key === "ArrowDown") moveDown();
        else if (event.key === "ArrowUp") rotate();
        draw();
      });

      function gameLoop() {
        moveDown();
        draw();
        setTimeout(gameLoop, 1000);
      }

      randomShape();
      gameLoop();
    </script>
  </body>
</html>
```

## html02

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>贪吃蛇游戏</title>
  </head>
  <body>
    <div id="gameArea"></div>
    <script type="text/javascript">
      const gameArea = document.getElementById("gameArea");
      const snake = [{ x: 200, y: 200 }];
      let direction = { x: 20, y: 0 };
      let food = { x: 100, y: 100 };
      let gameOver = false;

      function createDiv(className, x, y) {
        const div = document.createElement("div");
        div.className = className;
        div.style.left = `${x}px`;
        div.style.top = `${y}px`;
        gameArea.appendChild(div);
      }

      function draw() {
        gameArea.innerHTML = "";
        snake.forEach((segment) => createDiv("snake", segment.x, segment.y));
        createDiv("food", food.x, food.y);
      }

      function moveSnake() {
        const head = {
          x: snake[0].x + direction.x,
          y: snake[0].y + direction.y,
        };
        snake.unshift(head);

        // 检查是否撞到墙壁
        if (head.x < 0 || head.x >= 1200 || head.y < 0 || head.y >= 1200) {
          gameOver = true;
        }

        // 检查是否撞到自己
        for (let i = 1; i < snake.length; i++) {
          if (head.x === snake[i].x && head.y === snake[i].y) {
            gameOver = true;
          }
        }

        if (gameOver) {
          alert("游戏结束！");
          return;
        }

        if (head.x === food.x && head.y === food.y) {
          food = {
            x: Math.floor(Math.random() * 20) * 20,
            y: Math.floor(Math.random() * 20) * 20,
          };
        } else {
          snake.pop();
        }
      }

      function changeDirection(event) {
        const keyPressed = event.keyCode;
        const goingUp = direction.y === -20;
        const goingDown = direction.y === 20;
        const goingRight = direction.x === 20;
        const goingLeft = direction.x === -20;

        if (keyPressed === 37 && !goingRight) {
          direction = { x: -20, y: 0 };
        }
        if (keyPressed === 38 && !goingDown) {
          direction = { x: 0, y: -20 };
        }
        if (keyPressed === 39 && !goingLeft) {
          direction = { x: 20, y: 0 };
        }
        if (keyPressed === 40 && !goingUp) {
          direction = { x: 0, y: 20 };
        }
      }

      function gameLoop() {
        if (!gameOver) {
          setTimeout(() => {
            moveSnake();
            draw();
            gameLoop();
          }, 100);
        }
      }

      document.addEventListener("keydown", changeDirection);
      gameLoop();
    </script>
    <style type="text/css">
      body {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        margin: 0;
        background-color: #f0f0f0;
      }

      #gameArea {
        position: relative;
        width: 1200px;
        height: 1200px;
        background-color: #000;
      }

      .snake {
        position: absolute;
        width: 20px;
        height: 20px;
        background-color: #0f0;
      }

      .food {
        position: absolute;
        width: 20px;
        height: 20px;
        background-color: #f00;
      }
    </style>
  </body>
</html>
```

## html03

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>五子棋</title>
    <style>
      body {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        margin: 0;
        background-color: #f0f0f0;
      }
      canvas {
        background-color: #fff;
        border: 1px solid #000;
      }
    </style>
  </head>
  <body>
    <canvas id="gameCanvas" width="600" height="600"></canvas>
    <script type="text/javascript">
      const canvas = document.getElementById("gameCanvas");
      const ctx = canvas.getContext("2d");
      const size = 15;
      const box = canvas.width / size;
      let board = Array(size)
        .fill()
        .map(() => Array(size).fill(0));
      let currentPlayer = 1;

      function drawBoard() {
        for (let i = 0; i < size; i++) {
          for (let j = 0; j < size; j++) {
            ctx.strokeRect(i * box, j * box, box, box);
          }
        }
      }

      function drawPiece(x, y, player) {
        ctx.beginPath();
        ctx.arc(
          x * box + box / 2,
          y * box + box / 2,
          box / 2 - 2,
          0,
          2 * Math.PI
        );
        ctx.fillStyle = player === 1 ? "black" : "red";
        ctx.fill();
        ctx.closePath();
      }

      canvas.addEventListener("click", (e) => {
        const x = Math.floor(e.offsetX / box);
        const y = Math.floor(e.offsetY / box);
        if (board[x][y] === 0) {
          board[x][y] = currentPlayer;
          drawPiece(x, y, currentPlayer);
          if (checkWin(x, y, currentPlayer)) {
            alert(`Player ${currentPlayer} wins!`);
            board = Array(size)
              .fill()
              .map(() => Array(size).fill(0));
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            drawBoard();
          }
          currentPlayer = 3 - currentPlayer;
        }
      });

      function checkWin(x, y, player) {
        return (
          checkDirection(x, y, player, 1, 0) || // Horizontal
          checkDirection(x, y, player, 0, 1) || // Vertical
          checkDirection(x, y, player, 1, 1) || // Diagonal \
          checkDirection(x, y, player, 1, -1)
        ); // Diagonal /
      }

      function checkDirection(x, y, player, dx, dy) {
        let count = 1;
        for (let i = 1; i < 5; i++) {
          if (board[x + i * dx]?.[y + i * dy] === player) count++;
          else break;
        }
        for (let i = 1; i < 5; i++) {
          if (board[x - i * dx]?.[y - i * dy] === player) count++;
          else break;
        }
        return count >= 5;
      }

      drawBoard();
    </script>
  </body>
</html>
```
