<script lang="ts">
	export let backgroundOpacity: number = 0;

	const NOPAL_LOGO_SVG = `
    <svg width="30" height="30" viewBox="0 0 100 100" fill="none" xmlns="http://www.w3.org/2000/svg">
      <path d="M50 0L60.8088 24.1912L75.8088 19.1912L70.8088 39.1912L90.8088 34.1912L80.8088 50L90.8088 65.8088L70.8088 60.8088L75.8088 80.8088L60.8088 75.8088L50 100L39.1912 75.8088L24.1912 80.8088L29.1912 60.8088L9.19118 65.8088L19.1912 50L9.19118 34.1912L29.1912 39.1912L24.1912 19.1912L39.1912 24.1912L50 0Z" fill="currentColor"/>
    </svg>
  `;
	const MENU_ICON_SVG = `
    <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
      <path d="M3 12H21" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
      <path d="M3 6H21" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
      <path d="M3 18H21" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
    </svg>
  `;
</script>

<header style="--bg-opacity: {backgroundOpacity};">
	<div class="logo-container">
		{@html NOPAL_LOGO_SVG}
		<span class="logo-text">NOPAL</span>
	</div>
	<nav>
		{@html MENU_ICON_SVG}
	</nav>
</header>

<style>
	header {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 1rem 2rem;
		z-index: 1000;
		color: white;
		background-color: rgba(30, 30, 30, var(--bg-opacity));
		transition: background-color 0.2s ease-out;
	}

	.logo-container {
		display: flex;
		align-items: center;
		gap: 0.5rem;
	}

	.logo-text {
		font-size: 1.5rem;
		font-weight: bold;
	}
</style>
