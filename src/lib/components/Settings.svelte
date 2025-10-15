<script>
    import { classes } from "../stores/preferences";
</script>

<br />
<details>
    <summary>Settings</summary>

    <section>
        <h3>Classes</h3>
        {#each Object.entries($classes) as [period, name]}
            <div>
                <label>
                    Period {period}:
                    <input
                        type="text"
                        bind:value={$classes[period]}
                        placeholder="Enter class name"
                        size="25"
                    />
                </label>
            </div>
        {/each}
    </section>
</details>
