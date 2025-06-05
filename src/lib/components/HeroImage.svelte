<script lang="ts">
	export let scale: number = 1;
	export let top: number = 0; // For transform, will likely be 0 now
	export let left: number = 0; // For transform, will likely be 0 now
	export let opacity: number = 1; // New opacity prop

	export let positionStyle: 'fixed' | 'absolute' = 'fixed';
	export let topStyle: string = '0px'; // For actual top when absolute

	const heroImageUrl = "https://picsum.photos/seed/nopalhero2/1920/1080";
</script>

<div 
	class="hero-image-container" 
	style="position: {positionStyle}; top: {positionStyle === 'absolute' ? topStyle : '0'}; left: 0; width: 100vw; height: 100vh; z-index: 100; overflow: hidden; opacity: {opacity}; transition: opacity 0.3s ease-out;"
>
	<img
		src={heroImageUrl}
		alt="Hero"
		class="hero-image"
		style="transform: scale({scale}) translate({left}vw, {top}vh); transform-origin: center;"
	/>
</div>

<style>
	.hero-image {
		display: block; 
		width: 100%;
		height: 100%;
		object-fit: cover;
	}
</style>
