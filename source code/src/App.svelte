<script>
  import Grid from "./Grid.svelte";
  import { setClickType, entry, exit, setEntry, setExit } from "./stores.js";

  let rows = 6;
  let cols = 6;
  $: colors = (function() {
    let colors = new Array(rows);
    for (let i = 0; i < rows; i++) colors[i] = new Array(cols);
    return colors;
  })();

  $: if (window.innerHeight - 300 < 26 * rows) {
    rows = Math.floor((window.innerHeight - 300) / 26 - 1);
  }
  $: if (window.innerWidth < 32 * cols) {
    cols = Math.floor((window.innerWidth - 30) / 30 - 1);
  }

  function startBFS() {
    if (!entry || !exit) return;

    let bfsSearchInProgress = true;
    var [visited, parents] = initializeVisitsAndParents();

    var queue = [];
    var entryX = +entry.split("-")[0];
    var entryY = +entry.split("-")[1];
    var exitX = +exit.split("-")[0];
    var exitY = +exit.split("-")[1];

    queue.push(entryX + "-" + entryY);
    visited[entryX][entryY] = true;

    var stop = false;
    var delay = 0;
    while (queue.length && !stop) {
      var length = queue.length;

      var loopQueue = [];
      for (let k = 0; k < length; k++) {
        let value = queue.shift();

        let i = +value.split("-")[0];
        let j = +value.split("-")[1];

        if (i === exitX && j === exitY) {
          stop = true;
          break;
        }

        if (i + 1 < rows && !visited[i + 1][j] && colors[i + 1][j] !== "red") {
          queue.push(i + 1 + "-" + j);
          parents[i + 1][j] = i + "-" + j;
          visited[i + 1][j] = true;
        }
        if (i - 1 > -1 && !visited[i - 1][j] && colors[i - 1][j] !== "red") {
          queue.push(i - 1 + "-" + j);
          parents[i - 1][j] = i + "-" + j;
          visited[i - 1][j] = true;
        }
        if (j + 1 < cols && !visited[i][j + 1] && colors[i][j + 1] !== "red") {
          queue.push(i + "-" + (j + 1));
          parents[i][j + 1] = i + "-" + j;
          visited[i][j + 1] = true;
        }
        if (j - 1 > -1 && !visited[i][j - 1] && colors[i][j - 1] !== "red") {
          queue.push(i + "-" + (j - 1));
          parents[i][j - 1] = i + "-" + j;
          visited[i][j - 1] = true;
        }

        loopQueue.push(i + "-" + j);
      }

      let colorQueue = [...loopQueue];
      setTimeout(() => {
        colorQueue.forEach(box => {
          if (!(+box.split("-")[0] === entryX && +box.split("-")[1] === entryY))
            colors[+box.split("-")[0]][+box.split("-")[1]] = "darkgray";
        });

        colors = colors;
      }, 200 * delay);

      delay++;
    }

    setTimeout(() => {
      if (stop) {
        let x = exitX,
          y = exitY;
        let pathDelay = 0;
        [x, y] = [+parents[x][y].split("-")[0], +parents[x][y].split("-")[1]];
        if (entryX === x && entryY === y) return;
        do {
          let m = x,
            n = y;
          setTimeout(() => {
            colors[m][n] = "yellow";
            colors = colors;
          }, pathDelay++ * 100);
          [x, y] = [+parents[x][y].split("-")[0], +parents[x][y].split("-")[1]];
        } while (entryX !== x || entryY !== y);
      }
    }, 200 * delay);
  }

  function initializeVisitsAndParents() {
    var visited = new Array(rows);
    for (let i = 0; i < rows; i++) visited[i] = new Array(cols);

    var parents = new Array(rows);
    for (let i = 0; i < rows; i++) parents[i] = new Array(cols);

    return [visited, parents];
  }

  function randomMazeGenerator() {
    var mazeColors = new Array(rows);
    for (let i = 0; i < rows; i++) mazeColors[i] = new Array(cols);

    var x, y;
    x = Math.floor(Math.random() * rows);
    y = Math.floor(Math.random() * cols);
    mazeColors[x][y] = "blue";

    var a, b;
    do {
      a = Math.floor(Math.random() * rows);
      b = Math.floor(Math.random() * cols);
    } while (a === x && y === b);
    mazeColors[a][b] = "green";

    for (let i = 0; i < rows; i++)
      for (let j = 0; j < cols; j++)
        if (!mazeColors[i][j]) {
          if (Math.floor(Math.random() * 10) < 3) mazeColors[i][j] = "red";
          else mazeColors[i][j] = "white";
        }

    setEntry(x + "-" + y);
    setExit(a + "-" + b);
    colors = mazeColors;
  }

  function reset() {
    var mazeColors = [];
    for (let i = 0; i < rows; i++) {
      let colors = [];
      for (let j = 0; j < cols; j++) colors.push("");
      mazeColors.push(colors);
    }

    setEntry("");
    setExit("");
    colors = mazeColors;
  }
</script>

<style>
  main {
    text-align: center;
    margin: 0 auto;
  }

  h2 {
    margin-top: 0px;
  }

  .start {
    background-color: aqua;
  }
  
  button{
    border:1px solid black;
    border-radius: 5px;
    box-shadow: 2px 2px 6px 1px grey;
  }
</style>

<main>
  <h2>Shortest Path Finder</h2>
  Rows (6 to 30):
  <input type="range" bind:value={rows} min="6" max="30" />
  <br />
  Columns (6 to 60):
  <input type="range" bind:value={cols} min="6" max="60" />

  <Grid {colors} />

  <button on:click={startBFS} class="start">Start BFS</button>
  <br />
  <button on:click={randomMazeGenerator}>Generate Random Maze</button>
  <button on:click={reset}>Reset</button>
  <br />

  <button
    on:click={() => {
      setClickType('entry');
    }}>
    Place Entry
  </button>
  <button
    on:click={() => {
      setClickType('exit');
    }}>
    Place Exit
  </button>
  <button
    on:click={() => {
      setClickType('wall');
    }}>
    Place Wall
  </button>
  <button
    on:click={() => {
      setClickType('clear');
    }}>
    Clear Cell
  </button>

</main>
