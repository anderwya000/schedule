<script>
    import { onMount } from "svelte";
    import dayjs from "dayjs";
    import customParseFormat from "dayjs/plugin/customParseFormat";
    dayjs.extend(customParseFormat);

    // Toggle this to force a test time (for debugging)
    const FORCE_NOW = false;
    const FORCED_TIME = "08:00"; // HH:mm

    // If you were importing JSON, replace this with your import.
    // This inline sample ensures parsing/logic is isolated.
    const schedule = [
        { period: 1, type: "class", start: "07:20", end: "08:32" },
        { period: 2, type: "class", start: "08:32", end: "09:45" },
        { period: 3, type: "class", start: "09:50", end: "10:35" },
        { period: "Lunch", type: "break", start: "10:35", end: "11:00" }
    ];

    // Example class names (not required)
    const classNames = {
        1: "AP Human Geography",
        2: "Honors English",
        3: "Symphonic Band"
    };

    let currentPeriod = null;

    function parseTime(str) {
        // try both HH:mm and H:mm formats
        const parsed = dayjs(str, ["HH:mm", "H:mm"], true);
        return parsed.isValid() ? parsed : null;
    }

    onMount(() => {
        const update = () => {
            const now = FORCE_NOW ? dayjs(FORCED_TIME, "HH:mm") : dayjs();
            console.log("----- update -----");
            console.log("now:", now.format("HH:mm"));

            let found = null;
            for (const item of schedule) {
                const start = parseTime(item.start);
                const end = parseTime(item.end);

                // Defensive logs
                console.log("period:", item.period, "start:", item.start, "parsedStart:", start ? start.format("HH:mm") : "invalid", "end:", item.end, "parsedEnd:", end ? end.format("HH:mm") : "invalid");

                if (!start || !end) {
                    // If parsing failed we'll skip (and you'll see logs)
                    console.warn("Failed to parse times for period", item.period);
                    continue;
                }

                // inclusive start, exclusive end is common for schedules
                const isInRange = (now.isSame(start) || now.isAfter(start)) && now.isBefore(end);
                console.log("  in range?", isInRange);

                if (isInRange) {
                    found = item.period;
                    break;
                }
            }

            // Ensure reactivity — assign directly
            currentPeriod = found;
            console.log("-> detected currentPeriod:", currentPeriod);
        };

        update();
        const interval = setInterval(update, 30 * 1000); // every 30s while debugging
        return () => clearInterval(interval);
    });
</script>

<table id="schedule">
    <thead>
        <tr>
            <th>Period</th>
            <th style="width:70%">Class</th>
            <th>Start</th>
            <th>End</th>
        </tr>
    </thead>
    <tbody>
        {#each schedule as item}
            <!-- use == to avoid number/string mismatch -->
            <tr class:now={item.period == currentPeriod}>
                <td>{typeof item.period === "number" ? item.period + "" : item.period}</td>
                <td>{item.type === "class" ? (classNames[item.period] || item.period) : item.label || item.type}</td>
                <td>{item.start}</td>
                <td>{item.end}</td>
            </tr>
        {/each}
    </tbody>
</table>

<style>
    /* Force global so Svelte scoping won't rename selectors */
    :global(#schedule tr.now) {
        color: var(--ctp-mocha-blue, blue);
        font-weight: 700;
        background: color-mix(in srgb, var(--ctp-mocha-blue, blue) 14%, transparent);
    }

    /* small table niceties */
    #schedule { width: 100%; border-collapse: collapse; }
    #schedule th, #schedule td { padding: 6px 8px; border: 1px solid rgba(0,0,0,0.06); text-align: left; }
</style>
