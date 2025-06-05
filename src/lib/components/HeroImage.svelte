<script lang="ts">
	export let scale: number = 1;
	export let top: number = 0; // For transform, will likely be 0 now
	export let left: number = 0; // For transform, will likely be 0 now
	export let opacity: number = 1; // New opacity prop

	export let positionStyle: 'fixed' | 'absolute' = 'fixed';
	export let topStyle: string = '0px'; // For actual top when absolute

	const heroVideoUrl = "/horses.mp4";
</script>

<div 
	class="hero-image-container" 
	style="position: {positionStyle}; top: {positionStyle === 'absolute' ? topStyle : '0'}; left: 0; width: 100vw; height: 100vh; z-index: 100; overflow: hidden; opacity: {opacity}; transition: opacity 0.3s ease-out;"
>
	<video
		src={heroVideoUrl}
		class="hero-visual"
		autoplay
		muted
		loop
		playsinline
		preload="metadata"
		style="transform: scale({scale}) translate({left}vw, {top}vh); transform-origin: center;"
	>
		Your browser does not support the video tag.
	</video>
</div>

<style>
	.hero-visual {
		display: block;
		width: 100%;
		height: 100%;
		object-fit: cover; /* Ensures video fills width/height, maintaining aspect ratio, cropping if needed */
	}
</style>
