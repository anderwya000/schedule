<script>
    import { onMount } from "svelte";
    import dayjs from "dayjs";
    import { schedules } from "../data/schedules.js";

    export let scheduleId = "normal";
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

<div class="schedule">
    {#each schedule as block}
        <div
            class="class {isCurrentClass(block.start, block.end) ? 'now' : ''}"
        >
            <span class="time">{block.start} – {block.end}</span>
            <span class="name">
                {#if block.id.startsWith("class_")}
                    {userClasses[block.id]}
                {:else if block.id === "study_hall"}
                    Study Hall
                {:else if block.id === "sst"}
                    SST
                {:else if block.id === "lunch"}
                    Lunch
                {/if}
            </span>
        </div>
    {/each}
</div>

<style src="../styles/component.css"></style>
