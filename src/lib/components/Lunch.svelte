<script context="module">
    export const ssr = false;
</script>

<script>
    import { onMount } from "svelte";

    let rawData;
    let data;
    let error;
    let grouped;

    function processData(raw) {
        const now = new Date();
        const today = `${now.getFullYear()}-${String(now.getMonth() + 1).padStart(2, "0")}-${String(now.getDate()).padStart(2, "0")}`;

        let todayMeal = raw.data.find((m) => m.day === today);
        if (todayMeal) {
            todayMeal = JSON.parse(todayMeal.setting);
        }

        grouped = [];
        let currentCategory;
        if (todayMeal) {
            for (const item of todayMeal.current_display) {
                if (item.type === "category") {
                    currentCategory = { name: item.name, recipes: [] };
                    grouped.push(currentCategory);
                } else if (item.type === "recipe" && currentCategory) {
                    currentCategory.recipes.push(item.name);
                }
            }
        }
        return todayMeal;
    }

    onMount(async () => {
        try {
            const now = new Date();
            const year = now.getFullYear();
            const month = now.getMonth() + 1;

            const response = await fetch(
                `https://api.allorigins.win/get?url=${encodeURIComponent(`https://menus.healthepro.com/api/organizations/2455/menus/108947/year/${year}/month/${month}/date_overwrites`)}`,
            );

            if (!response.ok) {
                throw new Error(`HTTP ${response.status}`);
            }

            const fetched = await response.json();
            rawData = JSON.parse(fetched.contents);

            data = processData(rawData);
        } catch (e) {
            error = e;
            console.error(e);
        }
    });
</script>

<br />
<details>
    <summary>Lunch</summary>
    {#if error}
        <p style="color:red">{error}</p>
    {:else if data}
        {#each grouped as group}
            <h4>{group.name}</h4>
            <ul>
                {#each group.recipes as r}
                    <li>{r}</li>
                {/each}
            </ul>
        {/each}
    {:else}
        <p>Loading...</p>
    {/if}
</details>
