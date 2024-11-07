<script>
	import Icon from '@iconify/svelte';
	import { Notes, Calculator, Time, Applications, Settings, Minesweeper, Matrix } from '$lib';

	let isCalculatorVisible = false;
	let isNotesVisible = false;
	let isMinesweeperVisible = false;
	let isMatrixVisible = false;

	function toggleCalculator() {
		isCalculatorVisible = !isCalculatorVisible;
	}

	function toggleNotes() {
		isNotesVisible = !isNotesVisible;
	}

	function toggleMinesweeper() {
		isMinesweeperVisible = !isMinesweeperVisible;
	}

	function toggleMatrix() {
		isMatrixVisible = !isMatrixVisible;
	}

	// Reactive statement to compute the number of visible apps
	$: visibleAppsCount =
		(isMatrixVisible ? 1 : 0) +
		(isNotesVisible ? 1 : 0) +
		(isCalculatorVisible ? 1 : 0) +
		(isMinesweeperVisible ? 1 : 0) + // Add more apps as needed
		1; // +1 for the App component

	// Reactive statement to set the grid column class
	$: gridColsClass =
		visibleAppsCount > 3
			? 'grid-cols-2 grid-rows-2'
			: visibleAppsCount > 2
				? 'grid-cols-2'
				: 'grid-cols-1';
</script>

<div
	class="bg-[url('https://raw.githubusercontent.com/LinuxKits/Distro-wallpapers/refs/heads/main/Fedora/32/F32_default_wallpaper_day.png')] bg-cover bg-center"
>
	<div class="grid h-dvh grid-rows-[auto_1fr_auto] bg-base-100/75 backdrop-blur">
		<header
			class="sticky top-0 z-10 mx-2 grid grid-cols-[1fr_auto_1fr] items-center rounded-b bg-base-100/75 p-0.5 backdrop-blur"
		>
			<Applications />
			<Time />
			<Settings />
		</header>

		<main class={`grid ${gridColsClass} gap-2 overflow-hidden p-2`}>
			{#if isNotesVisible}
				<Notes />
			{/if}
			{#if isCalculatorVisible}
				<Calculator />
			{/if}
			{#if isMinesweeperVisible}
				<Minesweeper />
			{/if}
			{#if isMatrixVisible}
				<Matrix />
			{/if}
		</main>

		<footer class="sticky bottom-0 z-10 flex justify-center gap-2">
			<button on:click={toggleCalculator} class="btn btn-square rounded-b-none">
				<Icon icon="clarity:calculator-line" width="1.5rem" />
			</button>
			<button on:click={toggleNotes} class="btn btn-square rounded-b-none">
				<Icon icon="clarity:note-line" width="1.5rem" />
			</button>
			<button on:click={toggleMinesweeper} class="btn btn-square rounded-b-none">
				<Icon icon="clarity:flag-line" width="1.5rem" />
			</button>
			<button on:click={toggleMatrix} class="btn btn-square rounded-b-none">
				<Icon icon="clarity:flag-line" width="1.5rem" />
			</button>
		</footer>
	</div>
</div>
