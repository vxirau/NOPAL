<script lang="ts">
	export let backgroundOpacity: number = 0;

	// NOPAL_LOGO_SVG is removed as per request

	const MENU_ICON_SVG = `
    <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
      <path d="M3 12H21" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
      <path d="M3 6H21" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
      <path d="M3 18H21" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
    </svg>
  `;
</script>

<header style="--bg-opacity: {backgroundOpacity};">
	<div class="header-left">
		<span class="logo-text">NOPAL</span>
	</div>
	<div class="header-center">
		<img src="/favicon.png" alt="NOPAL Studios Favicon" class="header-favicon" />
	</div>
	<div class="header-right">
		<nav>
			{@html MENU_ICON_SVG}
		</nav>
	</div>
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
		transition: background-color 0.3s ease-out;
	}

	.header-left,
	.header-right {
		flex: 1;
		display: flex;
		align-items: center;
	}

	.header-left {
		justify-content: flex-start;
	}

	.header-right {
		justify-content: flex-end;
	}

	.header-center {
		flex-shrink: 0;
	}

	.header-favicon {
		height: 30px;
		width: 30px;
	}

	.logo-text {
		font-size: 1.5rem;
		font-weight: bold;
	}

	nav {
		cursor: pointer;
	}
</style>
