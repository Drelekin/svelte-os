<script lang="ts">
	import App from '$lib/components/App.svelte';
	import { onMount, onDestroy } from 'svelte';

	export let fontSize = 12;
	export let speed = 40;

	let canvas: HTMLCanvasElement;
	let ctx: CanvasRenderingContext2D;
	let offscreenCanvas: HTMLCanvasElement;
	let offscreenCtx: CanvasRenderingContext2D;
	let columns: number;
	let drops: Float32Array;
	let dropSpeeds: Float32Array;
	let dropIntensity: Float32Array;
	let frameId: number;
	let lastFrameTime = 0;
	let charCache: { [key: string]: ImageData } = {};
	let isLoading = true;

	const matrix = 'アイウエオカINDREKINDREK'.split('');

	const DROP_DENSITY = 0.4;
	const MIN_SPEED = 0.25;
	const MAX_SPEED = 1.5;
	const BRIGHTNESS_LEVELS = ['#008F11', '#00FF41'];

	let resizeObserver: ResizeObserver;

	onMount(() => {
		initCanvas();
		frameId = requestAnimationFrame(animate);

		resizeObserver = new ResizeObserver(() => {
			initCanvas();
		});
		resizeObserver.observe(canvas);

		window.addEventListener('resize', initCanvas);

		isLoading = false;

		return () => {
			window.removeEventListener('resize', initCanvas);
			resizeObserver.disconnect();
		};
	});

	onDestroy(() => {
		cancelAnimationFrame(frameId);
		resizeObserver.disconnect();
		isLoading = true;
	});

	function initCanvas() {
		canvas.width = canvas.offsetWidth;
		canvas.height = canvas.offsetHeight;
		ctx = canvas.getContext('2d', { alpha: false })!;

		offscreenCanvas = document.createElement('canvas');
		offscreenCanvas.width = canvas.width;
		offscreenCanvas.height = canvas.height;
		offscreenCtx = offscreenCanvas.getContext('2d', { alpha: false })!;

		columns = Math.floor(canvas.width / fontSize);
		drops = new Float32Array(columns).fill(1);
		dropSpeeds = new Float32Array(columns);
		dropIntensity = new Float32Array(columns);

		// Initialize with some drops already on screen
		for (let i = 0; i < columns; i++) {
			resetDrop(i, true);
		}

		initCharCache();
	}

	function resetDrop(index: number, initial = false) {
		drops[index] = initial ? (Math.random() * canvas.height) / fontSize : 0;
		dropSpeeds[index] = MIN_SPEED + Math.random() * (MAX_SPEED - MIN_SPEED);
		dropIntensity[index] = Math.floor(Math.random() * BRIGHTNESS_LEVELS.length);
	}

	function initCharCache() {
		const tempCanvas = document.createElement('canvas');
		const tempCtx = tempCanvas.getContext('2d')!;
		tempCanvas.width = fontSize;
		tempCanvas.height = fontSize;

		BRIGHTNESS_LEVELS.forEach((color) => {
			matrix.forEach((char) => {
				const key = `${char}_${color}`;
				tempCtx.clearRect(0, 0, fontSize, fontSize);
				tempCtx.fillStyle = color;
				tempCtx.font = `${fontSize}px monospace`;
				tempCtx.textBaseline = 'top';
				tempCtx.fillText(char, 0, 0);
				charCache[key] = tempCtx.getImageData(0, 0, fontSize, fontSize);
			});
		});
	}

	function animate(timestamp: number) {
		if (timestamp - lastFrameTime < speed) {
			frameId = requestAnimationFrame(animate);
			return;
		}
		lastFrameTime = timestamp;

		if (!isInViewport()) {
			frameId = requestAnimationFrame(animate);
			return;
		}

		offscreenCtx.fillStyle = 'rgba(0, 0, 0, 0.1)';
		offscreenCtx.fillRect(0, 0, canvas.width, canvas.height);

		// Create new drops
		for (let i = 0; i < columns; i++) {
			if (drops[i] <= 0 && Math.random() < DROP_DENSITY) {
				drops[i] = 1;
			}
		}

		// Draw characters
		for (let i = 0; i < drops.length; i++) {
			if (drops[i] > 0) {
				// Create trail effect
				for (let j = 0; j < 3; j++) {
					const y = Math.floor(drops[i] - j);
					if (y >= 0) {
						// Ensure we don't draw above the canvas
						const char = matrix[Math.floor(Math.random() * matrix.length)];
						const brightness =
							BRIGHTNESS_LEVELS[Math.min(dropIntensity[i] + (2 - j), BRIGHTNESS_LEVELS.length - 1)];
						const charData = charCache[`${char}_${brightness}`];

						offscreenCtx.putImageData(charData, i * fontSize, y * fontSize);
					}
				}

				drops[i] += dropSpeeds[i];

				if (drops[i] * fontSize > canvas.height) {
					if (Math.random() > 0.5) {
						resetDrop(i);
					} else {
						drops[i] = 0;
					}
				}
			}
		}

		// Add random new drops at top
		for (let i = 0; i < columns; i++) {
			if (drops[i] <= 0 && Math.random() < DROP_DENSITY) {
				resetDrop(i);
			}
		}

		ctx.drawImage(offscreenCanvas, 0, 0);
		frameId = requestAnimationFrame(animate);
	}

	function isInViewport(): boolean {
		const rect = canvas.getBoundingClientRect();
		return rect.top < window.innerHeight && rect.bottom > 0;
	}
</script>

<canvas
	bind:this={canvas}
	class="h-full w-full {isLoading ? 'opacity-0' : 'opacity-100'} transition-opacity"
></canvas>
