<script>
	import App from '$lib/components/App.svelte';
	import { onMount } from 'svelte';

	const ROWS = 4;
	const COLS = 4;
	const MINES = 1;

	/** @type {{ isMine: boolean, isRevealed: boolean, neighborMines: number, isFlagged: boolean }[][]} */
	let board = [];
	let gameOver = false;
	let win = false;
	let flagMode = false;

	function initializeBoard() {
		// Create empty board
		board = Array(ROWS)
			.fill()
			.map(() =>
				Array(COLS)
					.fill()
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

	function revealCell(row, col) {
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

	function toggleFlag(row, col) {
		if (gameOver || win || board[row][col].isRevealed) return;
		board[row][col].isFlagged = !board[row][col].isFlagged;
		board = [...board]; // Trigger reactivity
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

	function handleCellClick(row, col) {
		if (flagMode) {
			toggleFlag(row, col);
		} else {
			revealCell(row, col);
		}
	}

	function getCellContent(cell) {
		if (!cell.isRevealed) {
			return cell.isFlagged ? '🚩' : '';
		}
		if (cell.isMine) {
			return '💣';
		}
		return cell.neighborMines === 0 ? '' : cell.neighborMines;
	}

	function getCellClass(cell) {
		const baseClass = 'w-10 h-10 flex items-center justify-center border border-gray-400 font-bold';

		if (!cell.isRevealed) {
			return `${baseClass} bg-gray-300 hover:bg-gray-400`;
		}
		if (cell.isMine) {
			return `${baseClass} bg-red-500`;
		}

		const numberColors = [
			'',
			'text-blue-600',
			'text-green-600',
			'text-red-600',
			'text-blue-900',
			'text-red-900',
			'text-teal-600',
			'text-black',
			'text-gray-600'
		];

		return `${baseClass} bg-gray-100 ${numberColors[cell.neighborMines] || ''}`;
	}

	onMount(() => {
		initializeBoard();
	});
</script>

<App name="Minesweeper">
	<div>
		<div class="mb-4 space-x-4">
			<button
				on:click={initializeBoard}
				class="rounded bg-blue-500 px-4 py-2 text-white hover:bg-blue-600"
			>
				New Game
			</button>
			<button
				on:click={() => (flagMode = !flagMode)}
				class="px-4 py-2 {flagMode
					? 'bg-yellow-500'
					: 'bg-gray-500'} rounded text-white hover:bg-opacity-90"
			>
				{flagMode ? '🚩 Flag Mode' : 'Normal Mode'}
			</button>
		</div>

		{#if gameOver || win}
			<div class="mb-4 text-xl font-bold {win ? 'text-green-600' : 'text-red-600'}">
				{win ? 'You Win!' : 'Game Over!'}
			</div>
		{/if}

		<div class="inline-block border-4 border-gray-400">
			{#each board as row, rowIndex}
				<div class="flex">
					{#each row as cell, colIndex}
						<button
							on:click={() => handleCellClick(rowIndex, colIndex)}
							class={getCellClass(cell)}
							disabled={gameOver || win}
						>
							{getCellContent(cell)}
						</button>
					{/each}
				</div>
			{/each}
		</div>
	</div>
</App>
