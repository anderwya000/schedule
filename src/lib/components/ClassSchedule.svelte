<script>
    import { onMount } from "svelte";
    import dayjs from "dayjs";
    import { schedules } from "../data/schedules.js";

    export let scheduleId = "normal"; // "normal" or "sst"
    let schedule = [];
    let userClasses = {};

    function isCurrentClass(start, end) {
        const now = dayjs();
        const s = dayjs()
            .hour(+start.split(":")[0])
            .minute(+start.split(":")[1]);
        const e = dayjs().hour(+end.split(":")[0]).minute(+end.split(":")[1]);
        return now.isAfter(s) && now.isBefore(e);
    }

    onMount(() => {
        schedule = schedules[scheduleId];

        for (let i = 1; i <= 5; i++) {
            userClasses[`class_${i}`] =
                localStorage.getItem(`class_${i}`) || `Class ${i}`;
        }
    });
</script>

<table class="schedule">
    <thead>
        <tr>
            <th>Period</th>
            <th style="width:70%">Class</th>
            <th>Start</th>
            <th>End</th>
        </tr>
    </thead>
    <tbody>
        {#each schedule as block, index}
            <tr class={isCurrentClass(block.start, block.end) ? "now" : ""}>
                <td>
                    {#if block.id.startsWith("class_")}
                        {#if block.id[block.id.length - 1] === "1"}
                            {block.id[block.id.length - 1]}st
                        {:else if block.id[block.id.length - 1] === "2"}
                            {block.id[block.id.length - 1]}nd
                        {:else if block.id[block.id.length - 1] === "3"}
                            {block.id[block.id.length - 1]}rd
                        {:else}
                            {block.id[block.id.length - 1]}th
                        {/if}
                    {:else if block.id === "study_hall"}
                        SH
                    {:else if block.id === "sst"}
                        SST
                    {:else if block.id === "lunch"}
                        Lunch
                    {/if}
                </td>
                <td>
                    {#if block.id.startsWith("class_")}
                        {userClasses[block.id]}
                    {:else if block.id === "study_hall"}
                        Study Hall
                    {:else if block.id === "sst"}
                        SST
                    {:else if block.id === "lunch"}
                        Lunch
                    {/if}
                </td>
                <td>{block.start}</td>
                <td>{block.end}</td>
            </tr>
        {/each}
    </tbody>
</table>

<style src="../styles/component.css"></style>
