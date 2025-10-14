<script>
  import { onMount, onDestroy, tick } from "svelte";
  import dayjs from "dayjs";

  export let format = 'HH:mm:ss';
  let time = ""; 
  let interval;

  async function updateTime() {
    time = dayjs().format(format);
  }

  onMount(async () => {
    updateTime(); 
    interval = setInterval(updateTime, 1000);
  });

  onDestroy(() => clearInterval(interval));
</script>

<span class="clock">
    <span class="label">Time: </span>
    <div class="time">{@html time}</div>
</span>