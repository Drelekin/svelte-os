<script lang="ts">
	import { createEventDispatcher } from 'svelte';
	import Icon from '@iconify/svelte';
	import { scale } from 'svelte/transition';

	export let name: string;
	export let hasOverflow: boolean = false;
	export let className: string = '';

	const dispatch = createEventDispatcher();

	function handleClose() {
		dispatch('close');
	}
</script>

<div
	class="grid grid-rows-[auto_1fr] overflow-hidden rounded bg-base-100/75 shadow backdrop-blur {className}"
	in:scale={{ duration: 150 }}
>
	<header class="flex items-center justify-between gap-2 bg-base-100/75 py-0.5 pl-3.5 pr-0.5">
		<p class="text-sm font-medium">{name}</p>

		<button on:click={handleClose} class="btn btn-square btn-ghost btn-sm">
			<Icon icon="clarity:close-line" width="1.25rem" />
		</button>
	</header>
	<div class="grid w-full place-items-center {hasOverflow ? 'overflow-auto' : 'overflow-hidden'}">
		<slot />
	</div>
</div>
