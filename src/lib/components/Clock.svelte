<script>
  import { onMount } from 'svelte';
  import dayjs from 'dayjs';

  export let format = '[<span class="label">Time: </span><div class="time">]HH:mm:ss[</div>]';
  let time = '';

  let interval;

  onMount(() => {
    time = dayjs().format(format);
    interval = setInterval(() => {
      time = dayjs().format(format);
    }, 1000);

    return () => clearInterval(interval);
  });
</script>

<span class = "clock">{@html time}</span>
