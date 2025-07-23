<script>
  export let x;
  export let y;

  const draw = (ctx) => {
    ctx.fillStyle = 'red';
    ctx.fillRect(x, y, 50, 50); // Simple rectangle for the enemy
  };
</script>
