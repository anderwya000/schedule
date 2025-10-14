<script>
    import { onMount, onDestroy } from "svelte";
    import dayjs from "dayjs";
    import duration from "dayjs/plugin/duration";
    dayjs.extend(duration);

    export let schedule = [];
    let now = dayjs();
    let remaining;
    let interval;

    function getCurrentClass() {
        for (const item of schedule) {
            if (!item.start || !item.end) continue;
            const start = dayjs()
                .hour(+item.start.split(":")[0])
                .minute(+item.start.split(":")[1])
                .second(0);
            const end = dayjs()
                .hour(+item.end.split(":")[0])
                .minute(+item.end.split(":")[1])
                .second(0);
            if (now.isAfter(start) && now.isBefore(end)) return { start, end };
        }
        return null;
    }

    function updateRemaining() {
        now = dayjs();
        const current = getCurrentClass();
        if (!current) {
            remaining = "00:00:00";
            return;
        }
        const diffSec = current.end.diff(now, "second");
        const dur = dayjs.duration(Math.max(diffSec, 0), "seconds");
        remaining = dur.format
            ? dur.format("HH:mm:ss")
            : `${String(Math.floor(dur.asHours())).padStart(2, "0")}:${String(dur.minutes()).padStart(2, "0")}:${String(dur.seconds()).padStart(2, "0")}`;
    }

    onMount(() => {
        updateRemaining();
        interval = setInterval(updateRemaining, 1000);
    });

    onDestroy(() => clearInterval(interval));
</script>

<span class="clock">
    <span class="label">Left: </span>
    <div class="time">{remaining}</div>
</span>
