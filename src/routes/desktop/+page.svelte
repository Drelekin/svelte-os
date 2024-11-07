<script lang="ts">
	import Icon from '@iconify/svelte';
	import { Notes, Calculator, Time, Applications, Settings, Minesweeper, Matrix } from '$lib';
	import App from '$lib/components/App.svelte';

	let isCalculatorVisible = false;
	let isNotesVisible = false;
	let isMinesweeperVisible = false;
	let isMatrixVisible = false;

	// Keep track of visible apps order
	let visibleAppsOrder: string[] = [];

	function updateVisibleApps(name: string, isVisible: boolean) {
		if (isVisible) {
			// Add to order if not already present
			if (!visibleAppsOrder.includes(name)) {
				visibleAppsOrder = [...visibleAppsOrder, name];
			}
		} else {
			// Remove from order
			visibleAppsOrder = visibleAppsOrder.filter((app) => app !== name);
		}
	}

	function toggleCalculator() {
		isCalculatorVisible = !isCalculatorVisible;
		updateVisibleApps('Calculator', isCalculatorVisible);
	}

	function toggleNotes() {
		isNotesVisible = !isNotesVisible;
		updateVisibleApps('Notes', isNotesVisible);
	}

	function toggleMinesweeper() {
		isMinesweeperVisible = !isMinesweeperVisible;
		updateVisibleApps('Minesweeper', isMinesweeperVisible);
	}

	function toggleMatrix() {
		isMatrixVisible = !isMatrixVisible;
		updateVisibleApps('Matrix', isMatrixVisible);
	}

	$: visibleAppsCount = visibleAppsOrder.length;

	$: gridColsClass =
		visibleAppsCount >= 3
			? 'grid-cols-2 grid-rows-2'
			: visibleAppsCount >= 2
				? 'grid-cols-2'
				: 'grid-cols-1';

	$: components = [
		{ name: 'Notes', isVisible: isNotesVisible, toggle: toggleNotes, component: Notes },
		{
			name: 'Calculator',
			isVisible: isCalculatorVisible,
			toggle: toggleCalculator,
			component: Calculator,
			hasOverflow: true
		},
		{
			name: 'Minesweeper',
			isVisible: isMinesweeperVisible,
			toggle: toggleMinesweeper,
			component: Minesweeper,
			hasOverflow: true
		},
		{ name: 'Spoon', isVisible: isMatrixVisible, toggle: toggleMatrix, component: Matrix }
	];

	// Create a sorted components array based on visibility order
	$: sortedComponents = components.sort((a, b) => {
		if (!a.isVisible) return 1;
		if (!b.isVisible) return -1;
		return visibleAppsOrder.indexOf(a.name) - visibleAppsOrder.indexOf(b.name);
	});
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
			{#each sortedComponents as { name, isVisible, toggle, component, hasOverflow = false }, index}
				{console.log({ name, isVisible, toggle, component, hasOverflow, index })}
				{#if isVisible}
					<App
						{name}
						{hasOverflow}
						on:close={toggle}
						className={visibleAppsCount === 3 && index === 0 ? 'row-span-2' : ''}
					>
						<svelte:component this={component} />
					</App>
				{/if}
			{/each}
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
				<Icon icon="game-icons:spoon" width="1.5rem" />
			</button>
		</footer>
	</div>
</div>
