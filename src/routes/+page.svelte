<script lang="ts">
	import { onMount, onDestroy } from 'svelte';
	import { tweened } from 'svelte/motion';
	import { cubicOut } from 'svelte/easing';

	import PageHeader from '$lib/components/PageHeader.svelte';
	import HeroImage from '$lib/components/HeroImage.svelte';
	import ScrollTimeline from '$lib/components/ScrollTimeline.svelte';
	import NextContentSection from '$lib/components/NextContentSection.svelte';

	let scrollY = 0;
	let windowHeight = 0;
	let timelineInitialBottomRem = 2; // As per its fixed CSS `bottom: 2rem`
	let timelineHeightPx = 200; // As per its fixed CSS `height: 200px`

	// Animation phase durations
	const heroAnimationDuration = 500;
	const timelineFadeInDuration = 300;
	const timelineScrollActiveDuration = 2000;
	const timelineFadeOutDuration = 300;
	const nextSectionFadeInDuration = 300;
	const nextSectionContentHeight = 500; // Estimated height for the content of the next section
	const endScrollBuffer = 100; // Extra buffer at the very end

	// Calculate scroll boundaries for each phase
	const s0_heroEnd = heroAnimationDuration;
	const s1_timelineFadeInStart = s0_heroEnd;
	const s2_timelineFadeInEnd = s1_timelineFadeInStart + timelineFadeInDuration;
	const s3_timelineTrackScrollStart = s2_timelineFadeInEnd;
	const s4_timelineTrackScrollEnd = s3_timelineTrackScrollStart + timelineScrollActiveDuration;
	const s5_timelineFadeOutStart = s4_timelineTrackScrollEnd;
	const s6_timelineFadeOutEnd = s5_timelineFadeOutStart + timelineFadeOutDuration;
	const s7_nextSectionFadeInStart = s6_timelineFadeOutEnd;
	const s8_nextSectionFadeInEnd = s7_nextSectionFadeInStart + nextSectionFadeInDuration;

	// Define the point at which fixed elements unpin and become absolute
	const unpinScrollPosition = s8_nextSectionFadeInEnd;

	// Hero image animations
	const imageScale = tweened(1, { duration: 50, easing: cubicOut });
	const imageTop = tweened(0, { duration: 50, easing: cubicOut }); // Will be 0 for centered scaling
	const imageLeft = tweened(0, { duration: 50, easing: cubicOut }); // Will be 0 for centered scaling
	const heroOverallOpacity = tweened(1, { duration: 50, easing: cubicOut }); // For fading out hero
	const headerBackgroundOpacity = tweened(0, { duration: 50, easing: cubicOut });

	// Timeline animations
	const overallTimelineOpacity = tweened(0, { duration: 50, easing: cubicOut });
	const timelineSectionTranslateY = tweened(100, { duration: 50, easing: cubicOut });
	const timelineTrackTranslateX = tweened(0, { duration: 50, easing: cubicOut });

	// Next section animation
	const nextSectionOpacity = tweened(0, { duration: 50, easing: cubicOut });

	// Props for dynamic positioning of Hero and Timeline
	let heroPositionStyle: 'fixed' | 'absolute' = 'fixed';
	let heroTopStyle: string = '0px';
	let timelinePositionStyle: 'fixed' | 'absolute' = 'fixed';
	let timelineTopStyle: string = 'auto';

	function handleScroll() {
		scrollY = window.scrollY;

		// --- Hero Image State Management (Scale, Position, Opacity) ---
		if (scrollY <= s0_heroEnd) {
			// Phase 0: Hero Image Initial Scaling Animation
			const heroProgress = scrollY / s0_heroEnd;
			imageScale.set(1 - heroProgress * 0.5); // Scale from 1 down to 0.5
			imageTop.set(0); // Centered vertically during initial scale
			imageLeft.set(0); // Centered horizontally during initial scale
			heroOverallOpacity.set(1); // Fully visible
			headerBackgroundOpacity.set(heroProgress);
			heroPositionStyle = 'fixed';
		} else if (scrollY > s0_heroEnd && scrollY < s5_timelineFadeOutStart) {
			// Phase 1: Hero Image Scaled Down, Fixed, and Visible (before its fade-out)
			imageScale.set(0.5);
			imageTop.set(-10); // Positioned higher
			imageLeft.set(0); // Centered horizontally
			heroOverallOpacity.set(1); // Fully visible
			headerBackgroundOpacity.set(1); // Header fully opaque
			heroPositionStyle = 'fixed';
		} else if (scrollY >= s5_timelineFadeOutStart && scrollY < s6_timelineFadeOutEnd) {
			// Phase 2: Hero Image Fading Out
			imageScale.set(0.5);
			imageTop.set(-10); // Positioned higher
			imageLeft.set(0); // Centered horizontally
			const heroFadeOutProgress = (scrollY - s5_timelineFadeOutStart) / timelineFadeOutDuration;
			heroOverallOpacity.set(1 - heroFadeOutProgress); // Fading out
			headerBackgroundOpacity.set(1);
			heroPositionStyle = 'fixed';
		} else if (scrollY >= s6_timelineFadeOutEnd && scrollY < unpinScrollPosition) {
			// Phase 3: Hero Image Faded Out, Still Fixed (before unpinning)
			imageScale.set(0.5);
			imageTop.set(-10); // Positioned higher
			imageLeft.set(0); // Centered horizontally
			heroOverallOpacity.set(0); // Fully transparent
			headerBackgroundOpacity.set(1);
			heroPositionStyle = 'fixed';
		}

		// Note: The unpinning logic (scrollY >= unpinScrollPosition) will handle the final state for absolute positioning.
		// The 'else' for heroPositionStyle (line 163) handles setting it back to 'fixed' if scrollY < unpinScrollPosition.

		// Original logic for timeline and next section opacity/translation remains here, but
		// ensure hero related properties (like headerBackgroundOpacity) are not redundantly set if already handled above.
		// For instance, headerBackgroundOpacity is now fully managed by the hero state blocks above when hero is fixed.

		// Initial state for Timeline and Next Section if not yet animated (relevant for scrollY <= s0_heroEnd)
		if (scrollY <= s0_heroEnd) {
			overallTimelineOpacity.set(0);
			timelineSectionTranslateY.set(100);
			nextSectionOpacity.set(0);
		}

		// Determine Timeline Opacity and Vertical Translation
		let currentTimelineOpacity = 0;
		let currentTimelineTranslateY = 100;

		if (scrollY >= s1_timelineFadeInStart && scrollY < s2_timelineFadeInEnd) {
			const fadeInProgress = (scrollY - s1_timelineFadeInStart) / timelineFadeInDuration;
			currentTimelineOpacity = fadeInProgress;
			currentTimelineTranslateY = 100 * (1 - fadeInProgress);
		} else if (scrollY >= s2_timelineFadeInEnd && scrollY < s5_timelineFadeOutStart) {
			currentTimelineOpacity = 1;
			currentTimelineTranslateY = 0;
		} else if (scrollY >= s5_timelineFadeOutStart && scrollY < s6_timelineFadeOutEnd) {
			const fadeOutProgress = (scrollY - s5_timelineFadeOutStart) / timelineFadeOutDuration;
			currentTimelineOpacity = 1 - fadeOutProgress;
			currentTimelineTranslateY = 0; // Stays at 0 Y translation during its fade out
		} else if (scrollY >= s6_timelineFadeOutEnd) {
			currentTimelineOpacity = 0;
			currentTimelineTranslateY = 0; // Remains at 0 Y translation after fade out
		} else { // scrollY < s1_timelineFadeInStart (and also covers between s0_heroEnd and s1_timelineFadeInStart)
			currentTimelineOpacity = 0;
			currentTimelineTranslateY = 100;
		}
		overallTimelineOpacity.set(currentTimelineOpacity);
		timelineSectionTranslateY.set(currentTimelineTranslateY);

		// Timeline track horizontal scroll
		if (scrollY >= s3_timelineTrackScrollStart && scrollY < s4_timelineTrackScrollEnd) {
			const trackScrollProgress = (scrollY - s3_timelineTrackScrollStart) / timelineScrollActiveDuration;
			timelineTrackTranslateX.set(trackScrollProgress * -150);
		} else if (scrollY < s3_timelineTrackScrollStart) {
			timelineTrackTranslateX.set(0);
		} else { // scrollY >= s4_timelineTrackScrollEnd
			timelineTrackTranslateX.set(-150);
		}

		// Next section fade-in
		if (scrollY >= s7_nextSectionFadeInStart && scrollY < s8_nextSectionFadeInEnd) {
			const nextSectionProgress = (scrollY - s7_nextSectionFadeInStart) / nextSectionFadeInDuration;
			nextSectionOpacity.set(nextSectionProgress);
		} else if (scrollY < s7_nextSectionFadeInStart) {
			nextSectionOpacity.set(0);
		} else { // scrollY >= s8_nextSectionFadeInEnd
			nextSectionOpacity.set(1);
		}

		// Handle unpinning of Hero and Timeline
		if (scrollY >= unpinScrollPosition) {
			if (heroPositionStyle !== 'absolute') {
				// Lock hero animations to final state
				// With transform-origin: center, imageTop and imageLeft are 0 for centering.
				const finalHeroProgress = Math.min(1, unpinScrollPosition / s0_heroEnd);
				imageScale.set(1 - finalHeroProgress * 0.5);
				imageTop.set(0); // Was 0 for centering
				imageLeft.set(0); // Was 0 for centering
				heroOverallOpacity.set(0); // Should be faded out by unpinScrollPosition
				headerBackgroundOpacity.set(finalHeroProgress > 1 ? 1 : finalHeroProgress); // Header opacity based on hero animation completion

				// Calculate hero's top position when it becomes absolute
				// Since it's full viewport and transform-origin: center, its visual center is at viewport center.
				// When fixed, its container top is 0. So, its content (the image) is centered within that.
				// For unpinning, we effectively want to place its container at the scroll position.
				heroTopStyle = `${unpinScrollPosition}px`;
				heroPositionStyle = 'absolute';
			}

			if (timelinePositionStyle !== 'absolute') {
				// Lock timeline animations to final state
				overallTimelineOpacity.set(0); // Should be faded out by now
				timelineSectionTranslateY.set(0);
				timelineTrackTranslateX.set(-150);

				// Calculate timeline's top position. It was at bottom: 2rem, height: 200px.
				// Its visual top was windowHeight - (2rem converted to px) - 200px.
				const timelineFixedVisualTop =
					windowHeight - timelineInitialBottomRem * 16 - timelineHeightPx; // Assuming 1rem = 16px for calculation
				timelineTopStyle = `${unpinScrollPosition + timelineFixedVisualTop}px`;
				timelinePositionStyle = 'absolute';
			}
		} else {
			// Before unpinning, they are fixed
			if (heroPositionStyle !== 'fixed') heroPositionStyle = 'fixed';
			if (timelinePositionStyle !== 'fixed') timelinePositionStyle = 'fixed';
		}
	}

	onMount(() => {
		windowHeight = window.innerHeight;
		window.addEventListener('scroll', handleScroll);
		handleScroll();
		return () => {
			window.removeEventListener('scroll', handleScroll);
		};
	});

	const totalPageScrollHeight =
		s8_nextSectionFadeInEnd + nextSectionContentHeight + endScrollBuffer;
</script>

<svelte:head>
	<title>NOPAL Studios</title>
	<meta name="description" content="NOPAL Studios - Premier video production services. We bring your vision to life with creative storytelling and high-quality visuals." />
	<meta name="keywords" content="video production, film production, corporate video, music videos, commercials, post-production, NOPAL Studios" />
	<meta name="author" content="NOPAL Studios" />

	<!-- Open Graph / Facebook -->
	<meta property="og:type" content="website" />
	<meta property="og:url" content="https://www.nopalstudios.com/" />
	<meta property="og:title" content="NOPAL Studios" />
	<meta property="og:description" content="Premier video production services. Creative storytelling and high-quality visuals." />
	<meta property="og:image" content="https://www.nopalstudios.com/og-image.jpg" />

	<!-- Twitter -->
	<meta property="twitter:card" content="summary_large_image" />
	<meta property="twitter:url" content="https://www.nopalstudios.com/" />
	<meta property="twitter:title" content="NOPAL Studios" />
	<meta property="twitter:description" content="Premier video production services. Creative storytelling and high-quality visuals." />
	<meta property="twitter:image" content="https://www.nopalstudios.com/twitter-image.jpg" />
</svelte:head>

<PageHeader backgroundOpacity={$headerBackgroundOpacity} />

<HeroImage
	scale={$imageScale}
	top={$imageTop} 
	left={$imageLeft}
	opacity={$heroOverallOpacity}
	positionStyle={heroPositionStyle}
	topStyle={heroTopStyle}
/>

<ScrollTimeline
	opacity={$overallTimelineOpacity}
	translateY={$timelineSectionTranslateY}
	trackTranslateX={$timelineTrackTranslateX}
	positionStyle={timelinePositionStyle}
	topStyle={timelineTopStyle}
/>

<div class="scroll-spacer" style="height: {totalPageScrollHeight}px;"></div>

<NextContentSection opacity={$nextSectionOpacity} contentHeight={nextSectionContentHeight} />

<style>
	.scroll-spacer {
		width: 100%;
		position: relative;
		z-index: -10;
	}
</style>
