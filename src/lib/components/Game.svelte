<script>
  import { onMount } from 'svelte';
  import Player from './Player.svelte';
  import Enemy from './Enemy.svelte';
  import Bullet from './Bullet.svelte';

  const TIME_INTERVAL = 10;
  let ticks = 0;
  let playing = true;
  let canvas;
  let player = { x: 175, y: 550 };
  let bullets = [];
  let enemies = [];
  let score = 0;
  const LEFT = 0;
  const RIGHT = 1;
  const STOP = -1;
  let player_moving = STOP;
  let enemy_moving = RIGHT;

  const createEnemies = () => {
    let num_enemies = 30;
    let column_width = 60;
    let row_height = 70;
    let max_columns = 5;
    let c = 0;
    let r = 0;
    for (let i = 0; i < num_enemies; i++) {
      enemies.push({ x: c * column_width, y: r * row_height, alive: true });
      c++;
      if (c > max_columns) {
        c = 0;
        r++;
      }
    }
  };

  const reverse = () => {
    if (enemy_moving == LEFT) {
      enemy_moving = RIGHT;
    } else if (enemy_moving == RIGHT) {
      enemy_moving = LEFT;
    }
  }

  const update = () => {
    // move player
    if (player_moving == LEFT && player.x > 10) {
      player.x -= 10;
    } else if (player_moving == RIGHT && player.x < 740) {
      player.x += 10;
    } else {
      player_moving = STOP;
    }

    // Update bullet positions
    bullets = bullets.filter(bullet => bullet.y > 0);
    bullets.forEach(bullet => bullet.y -= 50);

    // Check for collisions
    bullets.forEach(bullet => {
      enemies.forEach(enemy => {
        if (bullet.x < enemy.x + 50 && bullet.x + 5 > enemy.x && bullet.y < enemy.y + 50 && bullet.y + 10 > enemy.y) {
          enemy.alive = false;
          score += 10;
          bullet.y = 0;
        }
      });
    });

    // move enemies
    let enemy_reverse = false;
    let leftmost = 700;
    let rightmost = 0;
    enemies.forEach(enemy => {
      if (enemy.x < leftmost) {
        leftmost = enemy.x;
      }
      if (enemy.x > rightmost) {
        rightmost = enemy.x;
      }
    });
    if (leftmost <= 10 || rightmost >= 680) {
      enemy_moving = enemy_moving == LEFT ? enemy_moving = RIGHT : enemy_moving = LEFT;
    }
    enemies.forEach(enemy => {
      if (enemy_moving == LEFT && !enemy_reverse) {
        if (enemy.x - 10 > 0) {
          enemy.x -= 10;
        } else {
          enemy_reverse = true;
        }
      }
      if (enemy_moving == RIGHT && !enemy_reverse) {
        if (enemy.x + 10 < 700) {
          enemy.x += 10;
        } else {
          enemy_reverse = true;
        }
      }
    })

    // Remove dead enemies
    enemies = enemies.filter(enemy => enemy.alive);
  };

  const shoot = () => {
    bullets.push({ x: player.x + 22, y: player.y });
  };

  const handleKeyDown = (event) => {
    if (event.key === 'ArrowLeft') {
      player_moving = LEFT;
    } else if (event.key === 'ArrowRight') {
      player_moving = RIGHT;
    } else if (event.key === ' ') {
      shoot();
    } else if (event.key === 'q') {
      playing = false;
    }
  };

  const draw = () => {
    const ctx = canvas.getContext('2d');
    ctx.clearRect(0, 0, canvas.width, canvas.height); // Clear the canvas

    // Draw player
    ctx.fillStyle = 'white';
    ctx.fillRect(player.x, player.y, 50, 20); // Simple rectangle for the player

    // Draw bullets
    bullets.forEach(bullet => {
      ctx.fillStyle = 'yellow';
      ctx.fillRect(bullet.x, bullet.y, 5, 10); // Simple rectangle for the bullet
    });

    // Draw enemies
    enemies.forEach(enemy => {
      if (enemy.alive) {
        ctx.fillStyle = 'red';
        ctx.fillRect(enemy.x, enemy.y, 50, 50); // Simple rectangle for the enemy
      }
    });

    // Draw score
    ctx.fillStyle = 'white';
    ctx.font = '20px Arial';
    ctx.fillText(`Score: ${score}`, 10, 20);
  };

  const play = () => {
    createEnemies();
    const interval = setInterval(() => {
      if (!playing) {
        clearInterval(interval);
      }
      update();
      draw();
    }, 100);
  };

  onMount(() => {
    window.addEventListener('keydown', handleKeyDown);
    play();
  });
</script>

<canvas bind:this={canvas} width="800" height="600"></canvas>

<style>
  canvas {
    background: black;
  }
</style>
