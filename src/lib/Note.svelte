<script lang="ts">
	import { createEventDispatcher } from 'svelte';
	const dispatch = createEventDispatcher();
	export let note: boolean;
</script>

<!-- svelte-ignore a11y-no-static-element-interactions -->
<!-- svelte-ignore a11y-click-events-have-key-events -->
<div class:active={note} on:click={() => dispatch('click')}>
	{#if note}
		Playing
	{:else}
		Muted
	{/if}
	<button on:click|stopPropagation={() => dispatch('delete')}>Delete</button>
</div>

<style>
	div {
		width: 50px;
		height: 50px;
		border: 1px solid #000;
		display: inline-block;
		margin: 5px;
	}
	.active {
		background: #0f0;
	}
</style>
