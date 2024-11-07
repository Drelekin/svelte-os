<script lang="ts">
	import App from '$lib/components/App.svelte';
	import { onMount } from 'svelte';

	const ROWS: number = 9;
	const COLS: number = 9;
	const MINES: number = 10;

	let board: {
		isMine: boolean;
		isRevealed: boolean;
		neighborMines: number;
		isFlagged: boolean;
	}[][] = [];
	let gameOver: boolean = false;
	let win: boolean = false;
	let flagMode: boolean = false;
	let mouseDown = false;

	function handleMouseDown() {
		mouseDown = true;
	}

	function handleMouseUp() {
		mouseDown = false;
	}

	function initializeBoard() {
		// Create empty board
		board = Array(ROWS)
			.fill(null)
			.map(() =>
				Array(COLS)
					.fill(null)
					.map(() => ({
						isMine: false,
						isRevealed: false,
						neighborMines: 0,
						isFlagged: false
					}))
			);

		// Place mines randomly
		let minesPlaced = 0;
		while (minesPlaced < MINES) {
			const row = Math.floor(Math.random() * ROWS);
			const col = Math.floor(Math.random() * COLS);
			if (!board[row][col].isMine) {
				board[row][col].isMine = true;
				minesPlaced++;
			}
		}

		// Calculate neighbor mines
		for (let row = 0; row < ROWS; row++) {
			for (let col = 0; col < COLS; col++) {
				if (!board[row][col].isMine) {
					let count = 0;
					// Check all 8 neighbors
					for (let i = -1; i <= 1; i++) {
						for (let j = -1; j <= 1; j++) {
							if (
								row + i >= 0 &&
								row + i < ROWS &&
								col + j >= 0 &&
								col + j < COLS &&
								board[row + i][col + j].isMine
							) {
								count++;
							}
						}
					}
					board[row][col].neighborMines = count;
				}
			}
		}

		gameOver = false;
		win = false;
		board = [...board]; // Trigger reactivity
	}

	function revealCell(row: number, col: number) {
		if (gameOver || win || board[row][col].isRevealed || board[row][col].isFlagged) return;

		if (board[row][col].isMine) {
			// Game Over
			revealAllMines();
			gameOver = true;
			return;
		}

		// Reveal current cell
		board[row][col].isRevealed = true;

		// If cell has no neighboring mines, reveal neighbors recursively
		if (board[row][col].neighborMines === 0) {
			for (let i = -1; i <= 1; i++) {
				for (let j = -1; j <= 1; j++) {
					if (
						row + i >= 0 &&
						row + i < ROWS &&
						col + j >= 0 &&
						col + j < COLS &&
						!board[row + i][col + j].isRevealed
					) {
						revealCell(row + i, col + j);
					}
				}
			}
		}

		board = [...board]; // Trigger reactivity
		checkWin();
	}

	function toggleFlag(row: number, col: number) {
		if (gameOver || win || board[row][col].isRevealed) return;
		board[row][col].isFlagged = !board[row][col].isFlagged;
		board = [...board]; // Trigger reactivity
	}

	function handleRightClick(event: MouseEvent, row: number, col: number) {
		event.preventDefault();
		if (!board[row][col].isRevealed) {
			board[row][col].isFlagged = !board[row][col].isFlagged;
		}
	}

	function revealAllMines() {
		for (let row = 0; row < ROWS; row++) {
			for (let col = 0; col < COLS; col++) {
				if (board[row][col].isMine) {
					board[row][col].isRevealed = true;
				}
			}
		}
		board = [...board]; // Trigger reactivity
	}

	function checkWin() {
		let hasWon = true;
		for (let row = 0; row < ROWS; row++) {
			for (let col = 0; col < COLS; col++) {
				if (!board[row][col].isMine && !board[row][col].isRevealed) {
					hasWon = false;
					break;
				}
			}
		}
		if (hasWon) {
			win = true;
		}
	}

	function handleCellClick(row: number, col: number) {
		if (flagMode) {
			toggleFlag(row, col);
		} else {
			revealCell(row, col);
		}
	}

	function getCellContent(cell: {
		isMine: boolean;
		isRevealed: boolean;
		neighborMines: number;
		isFlagged: boolean;
	}) {
		if (!cell.isRevealed) {
			return cell.isFlagged ? '🚩' : '';
		}
		if (cell.isMine) {
			return '💣';
		}
		return cell.neighborMines === 0 ? '' : cell.neighborMines;
	}

	function getCellClass(cell: {
		isMine: boolean;
		isRevealed: boolean;
		neighborMines: number;
		isFlagged: boolean;
	}) {
		if (!cell.isRevealed) {
			return `bg-gray-200 hover:bg-gray-300`;
		}
		if (cell.isMine) {
			return `bg-error`;
		}

		const numberColors = [
			'',
			'text-blue-700',
			'text-green-700',
			'text-red-700',
			'text-violet-700',
			'text-pink-700',
			'text-indigo-700',
			'text-stone-700',
			'text-amber-700'
		];

		return `bg-gray-300 cursor-default ${numberColors[cell.neighborMines] || ''}`;
	}

	onMount(() => {
		initializeBoard();
	});

	function newGame() {
		initializeBoard();
	}

	$: flaggedCount = board.flat().filter((cell) => cell.isFlagged).length;
</script>

<App name="Minesweeper" hasOverflow={true}>
	<div class="flex flex-col items-center justify-center p-3.5">
		<div class="grid w-full grid-cols-3 items-center">
			<p class="font-mono text-lg font-bold">
				💣 {MINES - flaggedCount}
			</p>
			<button on:click={newGame} class="btn btn-square btn-lg mx-auto">
				{#if gameOver || win}
					{win ? '🤩' : '💀'}
				{:else if mouseDown}
					😮
				{:else}
					🙂
				{/if}
			</button>
		</div>
		<div class="my-3.5 rounded bg-gray-500 p-1 shadow-xl">
			{#each board as row, rowIndex}
				<div class="flex">
					{#each row as cell, colIndex}
						<button
							on:contextmenu={(event) => handleRightClick(event, rowIndex, colIndex)}
							on:click={() => handleCellClick(rowIndex, colIndex)}
							on:mousedown={handleMouseDown}
							on:mouseup={handleMouseUp}
							on:mouseleave={handleMouseUp}
							class="{getCellClass(
								cell
							)} btn-square btn-sm m-[0.5px] rounded-none p-0 text-lg font-extrabold {gameOver ||
							win
								? 'pointer-events-none cursor-default'
								: ''}"
						>
							{getCellContent(cell)}
						</button>
					{/each}
				</div>
			{/each}
		</div>
		<button
			on:click={() => (flagMode = !flagMode)}
			class="btn {flagMode ? 'btn-outline btn-error' : ''}"
		>
			{flagMode ? '🚩 Flag Mode' : 'Normal Mode'}
		</button>
	</div>
</App>
