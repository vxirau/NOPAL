<script lang="ts">
	export let scale: number = 1;
	export let top: number = 0; // For transform, will likely be 0 now
	export let left: number = 0; // For transform, will likely be 0 now
	export let opacity: number = 1; // New opacity prop

	export let positionStyle: 'fixed' | 'absolute' = 'fixed';
	export let topStyle: string = '0px'; // For actual top when absolute

	const heroVideoUrl = '/video.mp4';

	let videoElement: HTMLVideoElement;
	export let videoDuration: number = 0;

	function handleLoadedMetadata() {
		if (videoElement) {
			videoDuration = videoElement.duration;
		}
	}

	export function playVideo() {
		videoElement?.play();
	}

	export function pauseVideo() {
		videoElement?.pause();
	}

	export function setVideoCurrentTime(time: number) {
		if (videoElement) {
			videoElement.currentTime = time;
		}
	}

	export function isVideoPaused(): boolean | undefined {
		if (videoElement) {
			return videoElement.paused;
		}
		return undefined;
	}

	export function getVideoDuration(): number {
		// console.log('[HeroImage] getVideoDuration START. videoElement:', videoElement, 'videoElement.duration:', videoElement?.duration, 'fallback videoDuration:', videoDuration);
		let durationToReturn: number = 0; // Default to 0

		if (
			videoElement &&
			typeof videoElement.duration === 'number' &&
			isFinite(videoElement.duration)
		) {
			durationToReturn = videoElement.duration;
			// console.log('[HeroImage] getVideoDuration returning videoElement.duration:', durationToReturn);
		} else {
			// console.log('[HeroImage] getVideoDuration falling back. Stored videoDuration:', videoDuration);
			if (typeof videoDuration === 'number' && isFinite(videoDuration)) {
				durationToReturn = videoDuration;
			} else {
				// This case should ideally not be hit if videoDuration is always a number
				// console.warn('[HeroImage] getVideoDuration: videoDuration prop is not a finite number, returning 0. videoDuration was:', videoDuration);
				durationToReturn = 0;
			}
			// console.log('[HeroImage] getVideoDuration returning from fallback:', durationToReturn);
		}
		// console.log('[HeroImage] getVideoDuration FINAL RETURN:', durationToReturn, 'typeof:', typeof durationToReturn);
		return durationToReturn;
	}
</script>

<div
	class="hero-image-container"
	style="position: {positionStyle}; top: {positionStyle === 'absolute'
		? topStyle
		: '0'}; left: 0; width: 100vw; height: 100vh; z-index: 100; overflow: hidden; opacity: {opacity}; transition: opacity 0.3s ease-out;"
>
	<video
		bind:this={videoElement}
		on:loadedmetadata={handleLoadedMetadata}
		src={heroVideoUrl}
		class="hero-visual"
		autoplay
		muted
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
