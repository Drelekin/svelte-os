<script lang="ts">
	import App from '$lib/components/App.svelte';

	let current = '';
	let previous = '';
	let operator = '';

	function clear() {
		current = '';
		previous = '';
		operator = '';
	}

	function appendNumber(number: string) {
		if (number === '.' && current.includes('.')) return;
		current = current.toString() + number.toString();
	}

	function chooseOperation(op: string) {
		if (current === '') {
			if (previous !== '') {
				operator = op;
			}
			return;
		}
		if (previous !== '') {
			compute();
		}
		operator = op;
		previous = current;
		current = '';
	}

	function compute() {
		let computation;
		const prev = parseFloat(previous);
		const curr = parseFloat(current);
		if (isNaN(prev) || isNaN(curr)) return;
		switch (operator) {
			case '+':
				computation = prev + curr;
				break;
			case '-':
				computation = prev - curr;
				break;
			case '*':
				computation = prev * curr;
				break;
			case '/':
				computation = prev / curr;
				break;
			default:
				return;
		}
		current = computation.toString();
		operator = '';
		previous = '';
	}

	function onkeydown(event: KeyboardEvent) {
		const key = event.key;
		if (key >= '0' && key <= '9') {
			appendNumber(key);
		} else if (key === '.') {
			appendNumber(key);
		} else if (key === '+' || key === '-' || key === '*' || key === '/') {
			chooseOperation(key);
		} else if (key === 'Enter') {
			compute();
		} else if (key === 'Escape') {
			clear();
		}
	}
</script>

<svelte:window on:keydown={onkeydown} />

<div class="mx-auto w-full max-w-md p-3.5 font-mono">
	<div
		class="mb-2 flex h-20 items-center rounded-lg bg-base-200/75 p-3.5 text-2xl font-bold shadow-inner backdrop-blur"
	>
		<p>
			{previous}
			{operator}
			{current}
		</p>
	</div>

	<div class="grid grid-flow-col grid-cols-4 grid-rows-5 gap-2">
		<button on:click={() => clear()} class="btn btn-lg">AC</button>
		<button on:click={() => appendNumber('7')} class="btn btn-lg">7</button>
		<button on:click={() => appendNumber('4')} class="btn btn-lg">4</button>
		<button on:click={() => appendNumber('1')} class="btn btn-lg">1</button>
		<button on:click={() => appendNumber('0')} class="btn btn-lg col-span-2">0</button>

		<button on:click={() => chooseOperation('/')} class="btn btn-lg">/</button>
		<button on:click={() => appendNumber('8')} class="btn btn-lg">8</button>
		<button on:click={() => appendNumber('5')} class="btn btn-lg">5</button>
		<button on:click={() => appendNumber('2')} class="btn btn-lg">2</button>

		<button on:click={() => chooseOperation('*')} class="btn btn-lg">*</button>
		<button on:click={() => appendNumber('9')} class="btn btn-lg">9</button>
		<button on:click={() => appendNumber('6')} class="btn btn-lg">6</button>
		<button on:click={() => appendNumber('3')} class="btn btn-lg">3</button>
		<button on:click={() => appendNumber('.')} class="btn btn-lg">.</button>

		<button on:click={() => chooseOperation('-')} class="btn btn-lg">-</button>
		<button on:click={() => chooseOperation('+')} class="btn btn-lg row-span-2 h-full"> + </button>
		<button on:click={() => compute()} class="btn btn-lg row-span-2 h-full">=</button>
	</div>
</div>
