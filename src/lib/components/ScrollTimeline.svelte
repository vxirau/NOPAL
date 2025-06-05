<script lang="ts">
	export let opacity: number = 0;
	export let translateY: number = 100; // For the initial slide-up effect when fixed
	export let trackTranslateX: number = 0;

	export let positionStyle: 'fixed' | 'absolute' = 'fixed';
	export let topStyle: string = 'auto'; // For actual top when absolute, 'auto' for bottom positioning when fixed

	const timelineTrackUrl = "https://picsum.photos/seed/timelinetrack/3000/150";

	$: containerStyle = `
		position: ${positionStyle};
		opacity: ${opacity};
		left: 0;
		width: 100%;
		height: 200px;
		background-color: rgba(10, 10, 10, 0.85);
		z-index: 200;
		display: flex;
		align-items: center;
		justify-content: center;
		${positionStyle === 'fixed' ? `bottom: 2rem; transform: translateY(${translateY}px);` : `top: ${topStyle}; transform: translateY(0px);`}
	`;
</script>

<div class="timeline-section" style={containerStyle}>
	<div class="timeline-track-container">
		<img 
			src={timelineTrackUrl} 
			alt="Timeline Track" 
			class="timeline-track"
			style="transform: translateX({trackTranslateX}vw);"
		/>
	</div>
	<div class="timeline-cursor"></div>
</div>

<style>
	.timeline-track-container {
		width: 100%;
		height: 150px;
		overflow: hidden;
		position: relative;
	}

	.timeline-track-container::before,
	.timeline-track-container::after {
		content: '';
		position: absolute;
		top: 0;
		height: 100%;
		width: 200px;
		z-index: 5;
	}
	.timeline-track-container::before {
		left: 0;
		background: linear-gradient(to right, rgba(10,10,10,1) 30%, rgba(10,10,10,0) 100%);
	}
	.timeline-track-container::after {
		right: 0;
		background: linear-gradient(to left, rgba(10,10,10,1) 30%, rgba(10,10,10,0) 100%);
	}

	.timeline-track {
		height: 100%;
		position: absolute;
		top: 0;
		left: 0;
	}

	.timeline-cursor {
		position: absolute;
		left: 50%;
		top: 0;
		width: 3px;
		height: 100%;
		background-color: #ff4500;
		transform: translateX(-50%);
		z-index: 10;
	}
</style>
