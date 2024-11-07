<script lang="ts">
	import App from '$lib/components/App.svelte';

	let content =
		"🚀 About\n\nI'm Indrek from Estonia. Working as a product manager for Loquiz.\n\nI wanted to build a website that doesn't follow the traditional flow of content.\nI find tiling window managers to be productive and I was curious what a website with similar\ndesign philosophy would look like.\n\nI built this using Svelte and Daisy. Design is inspired by tiling window managers like Hyprland\nand Regolith. The project also includes various apps and games that are there for fun.\n\n\n✉️ Contact\n\nindrek@leede.ee\ntwitch.tv/indrek\ndiscordapp.com/invite/kayXvMk";
	let textarea: HTMLTextAreaElement;

	function handleDownload() {
		const blob = new Blob([content], { type: 'text/plain' });
		const url = URL.createObjectURL(blob);
		const a = document.createElement('a');
		a.href = url;
		a.download = 'notes.txt';
		document.body.appendChild(a);
		a.click();
		document.body.removeChild(a);
		URL.revokeObjectURL(url);
	}

	function copyToClipboard() {
		navigator.clipboard.writeText(content).then(
			() => {
				alert('Content copied to clipboard');
			},
			(err) => {
				console.error('Could not copy text: ', err);
			}
		);
	}

	function clearContent() {
		if (confirm('Are you sure you want to clear all content?')) {
			content = '';
		}
	}

	function addToContent(contentAddition: string) {
		const start = textarea.selectionStart;
		const end = textarea.selectionEnd;

		content = content.slice(0, start) + contentAddition + content.slice(end);

		// Use setTimeout to ensure the DOM updates before setting the cursor position
		setTimeout(() => {
			textarea.focus();
			textarea.setSelectionRange(start + contentAddition.length, start + contentAddition.length);
		}, 0);
	}

	function addRandomEmoji() {
		const emojis = [
			'❤️',
			'✅',
			'✨',
			'😊',
			'⭐',
			'😭',
			'🛒',
			'😂',
			'💀',
			'🎉',
			'🎄',
			'🔥',
			'👍',
			'🫶',
			'🙏',
			'🎁',
			'👉',
			'🩷',
			'📍',
			'🚀'
		];
		const randomEmoji = emojis[Math.floor(Math.random() * emojis.length)];
		addToContent(randomEmoji);
	}

	function addRandomNumber() {
		const randomNumber = Math.floor(Math.random() * 100);
		addToContent(randomNumber.toString());
	}
</script>

<App name="Notes">
	<div class="grid h-full w-full grid-rows-[auto_1fr] px-3.5">
		<div class="flex items-center justify-between gap-2 py-3.5">
			<div class="flex gap-2">
				<button on:click={() => addToContent('👍')} class="btn">👍</button>
				<button on:click={() => addToContent('❤️')} class="btn">❤️</button>
				<button on:click={addRandomEmoji} class="btn">Surprise</button>
				<button on:click={addRandomNumber} class="btn">0 to 100</button>
			</div>
			<div class="flex gap-2">
				<button on:click={handleDownload} class="btn"> Download </button>
				<button on:click={copyToClipboard} class="btn"> Copy </button>
				<button on:click={clearContent} class="btn"> Clear </button>
			</div>
		</div>

		<label class="form-control">
			<textarea
				bind:value={content}
				bind:this={textarea}
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
