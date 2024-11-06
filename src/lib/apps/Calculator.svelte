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
		if (current === '' && previous !== '') {
			operator = op;
			return;
		}
		if (current === '') return;
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

<App name="Calculator">
	<div class="max-w-md p-3.5 font-mono">
		<div class="output mb-2 h-20 rounded bg-base-200 p-3.5 text-xl">
			<p class="previous-operand">
				{#if previous}
					{previous} {operator}
				{/if}
			</p>
			<p class="current-operand">
				{#if current}
					{current}
				{/if}
			</p>
		</div>

		<div class="grid grid-flow-col grid-cols-4 grid-rows-5 gap-2">
			<button class="btn" on:click={() => clear()}>AC</button>
			<button class="btn" on:click={() => appendNumber('7')}>7</button>
			<button class="btn" on:click={() => appendNumber('4')}>4</button>
			<button class="btn" on:click={() => appendNumber('1')}>1</button>
			<button class="btn col-span-2" on:click={() => appendNumber('0')}>0</button>

			<button class="btn" on:click={() => chooseOperation('/')}>/</button>
			<button class="btn" on:click={() => appendNumber('8')}>8</button>
			<button class="btn" on:click={() => appendNumber('5')}>5</button>
			<button class="btn" on:click={() => appendNumber('2')}>2</button>

			<button class="btn" on:click={() => chooseOperation('*')}>*</button>
			<button class="btn" on:click={() => appendNumber('9')}>9</button>
			<button class="btn" on:click={() => appendNumber('6')}>6</button>
			<button class="btn" on:click={() => appendNumber('3')}>3</button>
			<button class="btn" on:click={() => appendNumber('.')}>.</button>

			<button class="btn" on:click={() => chooseOperation('-')}>-</button>
			<button class="btn row-span-2 h-full" on:click={() => chooseOperation('+')}>+</button>
			<button class="btn row-span-2 h-full" on:click={() => compute()}>=</button>
		</div>
	</div>
</App>
