<script lang="ts">
	export let opacity: number = 0;
	export let contentHeight: number = 500; // Default height, can be overridden by prop
</script>

<div class="next-content-placeholder" style="opacity: {opacity}; --next-section-content-height: {contentHeight}px;">
	<h2>Next Section</h2>
	<p>This content appears after all the timeline animations are complete.</p>
	<p>You can scroll down to see it.</p>
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
