<script lang="ts">
	import Icon from '@iconify/svelte';

	export let name: string;
	export let hasOverflow: boolean = false;
	export let className: string = '';
</script>

<div
	class="grid grid-rows-[auto_1fr] overflow-hidden rounded bg-base-100/75 backdrop-blur {className}"
>
	<header class="flex items-center justify-between gap-2 bg-base-100/75 py-0.5 pl-3.5 pr-0.5">
		<p class="text-sm font-medium">{name}</p>

		<button class="btn btn-square btn-ghost btn-sm">
			<Icon icon="clarity:close-line" width="1.25rem" />
		</button>
	</header>
	<div class="grid w-full place-items-center {hasOverflow ? 'overflow-auto' : 'overflow-hidden'}">
		<slot />
	</div>
</div>
