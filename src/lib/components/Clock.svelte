<script>
    import { onMount, onDestroy } from "svelte";
    import dayjs from "dayjs";

    export let format = "[Time: ]HH:mm:ss";
    let time = "";

    let interval;

    onMount(() => {
        time = dayjs().format(format);
        interval = setInterval(() => {
            time = dayjs().format(format);
        }, 1000);

        return () => clearInterval(interval);
    });
</script>

<span class="clock">{time}</span>
