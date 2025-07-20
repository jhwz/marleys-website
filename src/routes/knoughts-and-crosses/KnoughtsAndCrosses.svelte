<script lang="ts">
	import Confetti from 'svelte-confetti';

	type Props = {};
	type Player = 'X' | 'O' | null;

	let gameState = $state<Player[]>(Array.from({ length: 9 }, () => null));
	let currentPlayer = $state<'X' | 'O'>('X');
	let gameOver = $state(false);
	let winner = $state<Player>(null);
	let {}: Props = $props();

	function format_player(player: Player): string {
		if (player === 'X') return '🎀';
		if (player === 'O') return '🧸';
		return '';
	}

	function makeMove(index: number) {
		if (gameState[index] || gameOver) return;

		gameState[index] = currentPlayer;

		// Check for winner
		const winningCombinations = [
			[0, 1, 2],
			[3, 4, 5],
			[6, 7, 8], // rows
			[0, 3, 6],
			[1, 4, 7],
			[2, 5, 8], // columns
			[0, 4, 8],
			[2, 4, 6] // diagonals
		];

		for (const combo of winningCombinations) {
			const [a, b, c] = combo;
			if (gameState[a] && gameState[a] === gameState[b] && gameState[a] === gameState[c]) {
				winner = gameState[a];
				gameOver = true;
				return;
			}
		}

		// Check for draw
		if (gameState.every((cell) => cell !== null)) {
			gameOver = true;
			return;
		}

		// Switch players
		currentPlayer = currentPlayer === 'X' ? 'O' : 'X';
	}

	function resetGame() {
		gameState = Array.from({ length: 9 }, () => null);
		currentPlayer = 'X';
		gameOver = false;
		winner = null;
	}

	function game_status() {
		if (winner) return `Player ${format_player(winner)} wins!`;
		if (gameOver) return "It's a draw!";
		return `Current player: ${format_player(currentPlayer)}`;
	}
</script>

<div class="game-container">
	<h3>Knoughts and Crosses</h3>

	<div class="status">
		{game_status()}
	</div>

	<game-grid>
		{#each gameState as cell, index}
			<button class="cell" onclick={() => makeMove(index)} disabled={cell !== null || gameOver}>
				{format_player(cell)}
			</button>
		{/each}
	</game-grid>

	{#if winner}
		<Confetti amount={100} cone y={[0, 2]} />
	{/if}

	<button class="reset-btn" onclick={resetGame}> Reset Game </button>
</div>

<style>
	.game-container {
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 1.5rem;
		padding: 2rem;
	}

	.status {
		font-size: 1.25rem;
		font-weight: 600;
		text-align: center;
	}

	game-grid {
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		grid-template-rows: repeat(3, 1fr);
		gap: 4px;
		background-color: #374151;
		border: 4px solid #374151;
		border-radius: 8px;
	}

	.cell {
		width: 100px;
		height: 100px;
		background-color: white;
		border: none;
		font-size: 2rem;
		font-weight: bold;
		color: #374151;
		cursor: pointer;
		transition: all 0.2s ease;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.cell:hover:not(:disabled) {
		background-color: #f3f4f6;
		transform: scale(1.05);
	}

	.cell:disabled {
		cursor: not-allowed;
	}

	.cell:focus {
		outline: 2px solid #2563eb;
		outline-offset: 2px;
	}

	@media (max-width: 480px) {
		.cell {
			width: 80px;
			height: 80px;
			font-size: 1.5rem;
		}

		h1 {
			font-size: 2rem;
		}
	}
</style>
