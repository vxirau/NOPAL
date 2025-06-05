<script lang="ts">
	export let opacity: number = 0;
	export let contentHeight: number = 500; // Default height, can be overridden by prop
</script>

<div
	class="next-content-placeholder"
	style="opacity: {opacity}; --next-section-content-height: {contentHeight}px;"
>
	<h2>Seccion inventada</h2>
	<p>Aqui habrá que poner la siguiente ;)</p>
	<p>Y seguiremos haciendo scroll</p>
</div>

<style>
	.next-content-placeholder {
		height: var(--next-section-content-height, 500px);
		padding: 2rem;
		background-color: #111;
		color: #ddd;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		text-align: center;
		position: relative;
	}
</style>
