<script>
  import Box from "./Box.svelte";
  export let colors = [];

</script>

{#each colors as colorRow, i}
  <div>
    {#each colorRow as colorCol, j}
      <Box x={i} y={j} colors={colors} color={colors[i][j]} />
    {/each}
  </div>
{/each}
