<script>
	import App from '$lib/components/App.svelte';
	import { onMount } from 'svelte';

	let content = '';

	// Auto-save to localStorage
	$: {
		if (typeof window !== 'undefined') {
			localStorage.setItem('notepad-content', content);
		}
	}

	onMount(() => {
		if (typeof window !== 'undefined') {
			const savedContent = localStorage.getItem('notepad-content');
			if (savedContent) {
				content = savedContent;
			}
		}
	});

	function handleDownload() {
		const blob = new Blob([content], { type: 'text/plain' });
		const url = URL.createObjectURL(blob);
		const a = document.createElement('a');
		a.href = url;
		a.download = 'notepad-content.txt';
		document.body.appendChild(a);
		a.click();
		document.body.removeChild(a);
		URL.revokeObjectURL(url);
	}

	function clearContent() {
		if (confirm('Are you sure you want to clear all content?')) {
			content = '';
		}
	}
</script>

<App name="Notes">
	<div class="grid h-full w-full grid-rows-[auto_1fr] px-3.5">
		<!-- Header -->
		<div class="mb-4 flex items-center justify-between">
			<h1>Notepad Editor</h1>
			<div class="space-x-2">
				<button
					on:click={handleDownload}
					class="rounded-lg bg-green-500 px-4 py-2 text-white transition-colors hover:bg-green-600"
				>
					💾 Save
				</button>
				<button
					on:click={clearContent}
					class="rounded-lg bg-red-500 px-4 py-2 text-white transition-colors hover:bg-red-600"
				>
					🗑️ Clear
				</button>
			</div>
		</div>

		<!-- Editor -->
		<label class="form-control">
			<div class="label">
				<span class="label-text">Your bio</span>
				<span class="label-text-alt">Alt label</span>
			</div>
			<textarea
				bind:value={content}
				class="textarea h-full resize-none"
				placeholder="Start typing here..."
			></textarea>
			<div class="label font-mono">
				<span class="label-text-alt">{content.length} characters</span>
				<span class="label-text-alt">
					{content.trim() ? content.trim().split(/\s+/).length : 0} words
				</span>
			</div>
		</label>
	</div>
</App>
