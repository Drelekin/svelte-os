<!-- MatrixRain.svelte -->
<script lang="ts">
	import App from '$lib/components/App.svelte';
	import { onMount, onDestroy } from 'svelte';

	let canvas: HTMLCanvasElement | null = null;
	let ctx: CanvasRenderingContext2D | null = null;
	let animationId: number;

	const chars: string[] = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789@#$%^&*()'.split('');
	const fontSize: number = 14;
	let columns: number;
	let drops: number[] = [];

	function initialize() {
		if (!canvas) return;

		canvas.width = window.innerWidth;
		canvas.height = window.innerHeight;

		columns = Math.floor(canvas.width / fontSize);
		drops = Array(columns).fill(1);
	}

	function draw() {
		if (!ctx) return;

		// Create fade effect
		ctx.fillStyle = 'rgba(0, 0, 0, 0.05)';
		ctx.fillRect(0, 0, canvas.width, canvas.height);

		ctx.fillStyle = '#0F0';
		ctx.font = `${fontSize}px monospace`;

		for (let i = 0; i < drops.length; i++) {
			const text = chars[Math.floor(Math.random() * chars.length)];
			ctx.fillText(text, i * fontSize, drops[i] * fontSize);

			if (drops[i] * fontSize > canvas.height && Math.random() > 0.975) {
				drops[i] = 0;
			}

			drops[i]++;
		}

		animationId = requestAnimationFrame(draw);
	}

	onMount(() => {
		if (canvas) {
			ctx = canvas.getContext('2d');
			initialize();
			draw();
		}

		window.addEventListener('resize', initialize);

		return () => {
			cancelAnimationFrame(animationId);
			window.removeEventListener('resize', initialize);
		};
	});
</script>

<App name="Matrix Rain">
	<canvas bind:this={canvas} class="h-full w-full"></canvas>
</App>
