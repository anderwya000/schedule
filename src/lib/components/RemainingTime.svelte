<script>
  import { onMount, onDestroy } from "svelte";
  import dayjs from "dayjs";

  export let schedule = [];
  let now = dayjs();
  let interval;
  let remaining = "00:00:00";

  function getTimeObj(timeStr) {
    const [h, m] = timeStr.split(":").map(Number);
    return now.hour(h).minute(m).second(0);
  }

  function currentClass() {
    for (const item of schedule) {
      if (!item.start || !item.end) continue;
      const start = getTimeObj(item.start);
      const end = getTimeObj(item.end);
      if (now.isAfter(start) && now.isBefore(end)) return item;
    }
    return null;
  }

  function updateRemaining() {
    now = dayjs();
    const current = currentClass();
    if (!current) {
      remaining = "00:00:00";
      return;
    }
    const end = getTimeObj(current.end);
    let diff = end.diff(now, "second");
    if (diff < 0) diff = 0;
    const hours = Math.floor(diff / 3600).toString().padStart(2, "0");
    const minutes = Math.floor((diff % 3600) / 60).toString().padStart(2, "0");
    const seconds = (diff % 60).toString().padStart(2, "0");
    remaining = `${hours}:${minutes}:${seconds}`;
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
