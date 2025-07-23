<script>
  import { onMount } from 'svelte';
  import Player from './Player.svelte';
  import Enemy from './Enemy.svelte';
  import Bullet from './Bullet.svelte';

  let canvas;
  let player = { x: 175, y: 550 };
  let bullets = [];
  let enemies = [];
  let score = 0;

  const createEnemies = () => {
    for (let i = 0; i < 5; i++) {
      enemies.push({ x: i * 60, y: 0, alive: true });
    }
  };

  const update = () => {
    // Update bullet positions
    bullets = bullets.filter(bullet => bullet.y > 0);
    bullets.forEach(bullet => bullet.y -= 5);

    // Check for collisions
    bullets.forEach(bullet => {
      enemies.forEach(enemy => {
        if (bullet.x < enemy.x + 50 && bullet.x + 5 > enemy.x && bullet.y < enemy.y + 50 && bullet.y + 10 > enemy.y) {
          enemy.alive = false;
          score += 10;
        }
      });
    });

    // Remove dead enemies
    enemies = enemies.filter(enemy => enemy.alive);
  };

  const shoot = () => {
    bullets.push({ x: player.x + 22, y: player.y });
  };

  const handleKeyDown = (event) => {
    if (event.key === 'ArrowLeft' && player.x > 0) {
      player.x -= 10;
    } else if (event.key === 'ArrowRight' && player.x < 350) {
      player.x += 10;
    } else if (event.key === ' ') {
      shoot();
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

  onMount(() => {
    createEnemies();
    window.addEventListener('keydown', handleKeyDown);
    const interval = setInterval(() => {
      update();
      draw();
    }, 100);
    return () => {
      clearInterval(interval);
      window.removeEventListener('keydown', handleKeyDown);
    };
  });
</script>

<canvas bind:this={canvas} width="400" height="600"></canvas>

<style>
  canvas {
    background: black;
  }
</style>
