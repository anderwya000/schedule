<script>
  import { onMount, onDestroy } from 'svelte';
  import dayjs from 'dayjs';

  export let format = 'HH:mm:ss';
  let time = '';

  let interval;

  onMount(() => {
    // Initialize time right away on mount (client-side only)
    time = dayjs().format(format);
    interval = setInterval(() => {
      time = dayjs().format(format);
    }, 1000);

    return () => clearInterval(interval);
  });
</script>

<span class = "clock">{time}</span>
