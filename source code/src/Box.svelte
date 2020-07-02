<script>
  import { clickType, entry, exit, setEntry, setExit } from "./stores.js";
  export let x, y, colors, color;

  function handleClick() {
    if (clickType === "wall") {
      colors[x][y] = "red";
      this.style.backgroundColor = "red";
    } else if (clickType === "entry") {
      if (entry !== "") {
        colors[entry.split("-")[0]][entry.split("-")[1]] = "";
        document.getElementById(
          entry.split("-")[0] + "-" + entry.split("-")[1]
        ).style.backgroundColor = "";
      }
      colors[x][y] = "blue";
      setEntry(x + "-" + y);
      this.style.backgroundColor = "blue";
    } else if (clickType === "exit") {
      if (exit !== "") {
        colors[exit.split("-")[0]][exit.split("-")[1]] = "";
        document.getElementById(
          exit.split("-")[0] + "-" + exit.split("-")[1]
        ).style.backgroundColor = "";
      }
      colors[x][y] = "green";
      setExit(x + "-" + y);
      this.style.backgroundColor = "green";
    } else {
      colors[x][y] = "";
      this.style.backgroundColor = "";
    }
  }
</script>

<style>
  .box {
    width: 25px;
    height: 25px;
    border: 1px solid black;
    display: inline-block;
    margin: 0px 2px;
  }
</style>

<div
  class="box"
  on:click={handleClick}
  id={x + '-' + y}
  style="background-color:{color}" />
