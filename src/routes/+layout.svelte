<script lang="ts">
	import '../app.css';
	let { children } = $props();
</script>

<svelte:head>
	<title>神経衰弱 - Shinkei Suijaku (Powerd by sveltekit)</title>
</svelte:head>
<div class="align-center flex h-screen flex-col justify-center text-center">
	{@render children()}
</div>
