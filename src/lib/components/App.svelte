<script lang="ts">
	export let name: string;
</script>

<div class="grid grid-rows-[auto_1fr] overflow-hidden rounded bg-base-100/75 backdrop-blur">
	<header class="flex items-center justify-between gap-2 bg-base-100/75 py-0.5 pl-3.5 pr-0.5">
		<p class="text-sm font-medium">{name}</p>

		<button class="btn btn-square btn-ghost btn-sm"> x </button>
	</header>
	<div class="overflow-auto">
		<div class="h-[2000px]">
			<slot />
		</div>
	</div>
</div>
