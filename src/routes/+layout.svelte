<script>
	import '$lib/styles/component.css';
    import favicon from '$lib/assets/favicon.png'
	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
    <title>Schedule</title>
</svelte:head>

{@render children?.()}
