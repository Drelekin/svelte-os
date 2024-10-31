<script>
	import Icon from '@iconify/svelte';
	import { Notes, Calculator } from '$lib';

	let isCalculatorVisible = false;
	let isNotesVisible = false;

	function toggleCalculator() {
		isCalculatorVisible = !isCalculatorVisible;
	}

	function toggleNotes() {
		isNotesVisible = !isNotesVisible;
	}

	// Reactive statement to compute the number of visible apps
	$: visibleAppsCount = (isNotesVisible ? 1 : 0) + (isCalculatorVisible ? 1 : 0) + 1; // +1 for the App component

	// Reactive statement to set the grid column class
	$: gridColsClass = visibleAppsCount > 2 ? 'grid-cols-2' : 'grid-cols-1';
</script>

<div
	class="bg-[url('https://raw.githubusercontent.com/LinuxKits/Distro-wallpapers/refs/heads/main/Fedora/32/F32_default_wallpaper_day.png')] bg-cover bg-center"
>
	<div class="grid h-dvh grid-rows-[auto_1fr_auto] bg-base-100/75 backdrop-blur">
		<header class="sticky top-0 z-10 mx-2 grid grid-cols-3 rounded-b bg-base-100/75 backdrop-blur">
			<div>left</div>
			<div>center</div>
			<div>right</div>
		</header>

		<main class={`grid ${gridColsClass} gap-2 overflow-hidden p-2`}>
			{#if isNotesVisible}
				<Notes />
			{/if}
			{#if isCalculatorVisible}
				<Calculator />
			{/if}
		</main>

		<footer class="sticky bottom-0 z-10 flex justify-center gap-2">
			<button on:click={toggleCalculator} class="btn btn-square rounded-b-none">
				<Icon icon="clarity:calculator-line" width="1.5rem" />
			</button>
			<button on:click={toggleNotes} class="btn btn-square rounded-b-none">
				<Icon icon="clarity:calculator-line" width="1.5rem" />
			</button>
		</footer>
	</div>
</div>
