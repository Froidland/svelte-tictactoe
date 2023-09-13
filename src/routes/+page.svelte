<script lang="ts">
	type Player = 'X' | 'O';
	type Board = Cell[][];
	type Cell = Player | undefined;

	let boardSize = 3;
	let emptyCells: number;
	let currentPlayer: Player;
	let gameWon = false;
	let board: Board = generateBoard(boardSize, 'X');

	function generateBoard(boardSize: number, startingPlayer: Player): Board {
		const newBoard: Board = [];

		for (let row = 0; row < boardSize; row++) {
			newBoard.push([...Array(boardSize)]);
		}

		currentPlayer = startingPlayer;
		gameWon = false;
		emptyCells = boardSize * boardSize;

		return newBoard;
	}

	function makeMove(row: number, col: number) {
		if (gameWon) {
			return;
		}

		if (board[row][col] === undefined) {
			board[row][col] = currentPlayer;
			emptyCells -= 1;

			if (checkWin(board)) {
				gameWon = true;
				return;
			}

			currentPlayer = currentPlayer === 'X' ? 'O' : 'X';
		}
	}

	function horizontalCheck(board: Board) {
		for (const row of board) {
			const rowCheck = new Set(row);

			if (rowCheck.size === 1 && !rowCheck.has(undefined)) {
				return true;
			}
		}

		return false;
	}

	function verticalCheck(board: Board) {
		for (let i = 0; i < boardSize; i++) {
			const firstValue = board[0][i];

			if (firstValue === undefined) {
				continue;
			}

			for (let j = 1; j < boardSize; j++) {
				if (board[j][i] !== firstValue) {
					break;
				}

				if (j === boardSize - 1) {
					return true;
				}
			}
		}

		return false;
	}

	function diagonalCheck(board: Board) {
		let firstValue = board[0][0];

		// top-left to bottom-right
		if (firstValue !== undefined) {
			for (let i = 1; i < boardSize; i++) {
				if (board[i][i] !== firstValue) {
					break;
				}

				if (i === boardSize - 1) {
					return true;
				}
			}
		}

		firstValue = board[0][boardSize - 1];
		let i = 1;
		let j = boardSize - 2;

		// top-right to bottom-left
		if (firstValue !== undefined) {
			while (j >= 0) {
				if (board[i][j] !== firstValue) {
					break;
				}

				if (j === 0) {
					return true;
				}

				i++;
				j--;
			}
		}

		return false;
	}

	function checkWin(board: Board) {
		if (horizontalCheck(board)) {
			return true;
		}

		if (verticalCheck(board)) {
			return true;
		}

		if (diagonalCheck(board)) {
			return true;
		}

		return false;
	}
</script>

<svelte:head>
	<title>Yes.</title>
</svelte:head>

<div class="flex flex-col justify-center items-center gap-4 h-screen bg-zinc-900 text-white">
	<div class="flex items-center gap-4">
		<button
			class="bg-red-900 text-white px-4 py-2 rounded hover:bg-red-800"
			on:click={() => (board = generateBoard(boardSize, 'X'))}>Reset board</button
		>
	</div>
	<div>
		{#each board as row, r}
			<div class="flex">
				{#each row as cell, c}
					<!-- svelte-ignore a11y-click-events-have-key-events -->
					<!-- svelte-ignore a11y-no-static-element-interactions -->
					<div
						class="flex items-center justify-center border-white border-[1px] w-[100px] h-[100px] select-none text-6xl font-bold"
						on:click={() => makeMove(r, c)}
					>
						{cell ? cell : ''}
					</div>
				{/each}
			</div>
		{/each}
	</div>
	<div class="absolute text-4xl font-bold text-center bottom-56">
		{#if gameWon}
			<p><span class="text-pink-700">{currentPlayer}</span> has won the game!</p>
		{/if}
		{#if emptyCells === 0 && !gameWon}
			<p>The game has ended in a draw!</p>
		{/if}
	</div>
</div>
