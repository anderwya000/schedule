<script>
    import { onMount } from "svelte";
    import sched_normal from "../data/normal.json";
    import sched_sst from "../data/sst.json";
    import dayjs from "dayjs";

    const classNames = {
        1: "AP Human Geography",
        2: "Honors English",
        3: "Symphonic Band",
        4: "AP Computer Science A",
        5: "Physical Education",
    };
    const schedule = dayjs().day() == 2 ? sched_normal : sched_sst;

    let now = dayjs();
    const timer = setInterval(() => (now = dayjs()), 1000);

    function ordinal_suffix_of(i) {
        let j = i % 10,
            k = i % 100;
        if (j === 1 && k !== 11) {
            return i + "st";
        }
        if (j === 2 && k !== 12) {
            return i + "nd";
        }
        if (j === 3 && k !== 13) {
            return i + "rd";
        }
        return i + "th";
    }
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
            <tr>
                <td>
                    {#if typeof item.period === "number"}
                        {ordinal_suffix_of(item.period)}
                    {:else}
                        {item.period}
                    {/if}
                </td>
                <td>
                    {#if item.type === "class"}
                        {classNames[item.period] || item.period}
                    {:else}
                        {item.label}
                    {/if}
                </td>
                <td>{item.start}</td>
                <td>{item.end}</td>
            </tr>
        {/each}
    </tbody>
</table>
