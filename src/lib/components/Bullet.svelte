<script>
  export let x;
  export let y;

  const draw = (ctx) => {
    ctx.fillStyle = 'yellow';
    ctx.fillRect(x, y, 5, 10); // Simple rectangle for the bullet
  };
</script>
