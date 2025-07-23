<script>
  export let x;
  export let y;

  const draw = (ctx) => {
    ctx.fillStyle = 'white';
    ctx.fillRect(x, y, 50, 20); // Simple rectangle for the player
  };
</script>
