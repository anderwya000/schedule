<script>
	import '$lib/styles/component.css';
	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href="/schedule/favicon.png" />
    <title>Schedule</title>
</svelte:head>

{@render children?.()}
