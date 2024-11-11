<script lang="ts">
	import { Button } from '$lib/components/ui/button';
	import { emojis } from './emojis';

	type GameState = 'start' | 'playing' | 'pause' | 'won' | 'lost';
	let gameState = $state<GameState>('playing');
	let selected = $state<number[]>([]);
	let matches = $state<string[]>([]);
	const size = 20;
	const maxMatches = size / 2;
	let grid = $state<string[]>(initGrid());

	$effect(() => {
		if (selected.length === 2) {
			matchCards();
		}
	});

	$effect(() => {
		if (maxMatches === matches.length) {
			gameWon();
		}
	});

	function selectCard(cardIndex: number) {
		selected = selected.concat(cardIndex);
	}

	function matchCards() {
		const [card1, card2] = selected;
		if (grid[card1] === grid[card2]) {
			const card = grid[card1];
			matches = matches.concat(card);
			selected = [];
		}

		setTimeout(() => {
			selected = [];
		}, 500);
	}

	function resetGame() {
		grid = initGrid();
		matches = [];
		selected = [];
	}

	function gameWon() {
		gameState = 'won';
		resetGame();
	}

	function initGrid() {
		const cards = new Set<string>();
		const maxSize = size / 2;

		while (cards.size < maxSize) {
			const randomIndex = Math.floor(Math.random() * emojis.length);
			cards.add(emojis[randomIndex]);
		}

		return shuffle([...cards, ...cards]);
	}

	function shuffle<Item>(items: Item[]) {
		return items.sort(() => Math.random() - 0.5);
	}
</script>

{#if gameState === 'start'}
	<h1 class="mb-5 text-2xl font-bold text-gray-900 md:text-5xl lg:text-6xl">
		Welcome to <span
			class="bg-gradient-to-r from-sky-400 to-emerald-600 bg-clip-text text-transparent"
			>Shinkei Suijaku</span
		>
	</h1>
	<Button class="self-center" aria-label="play-game" onclick={() => (gameState = 'playing')}
		>Play The Game</Button
	>
{/if}

{#if gameState === 'playing'}
	<h1 class="text-2xl font-bold text-gray-900 md:text-5xl lg:text-6xl">
		Playing <span class="bg-gradient-to-r from-sky-400 to-emerald-600 bg-clip-text text-transparent"
			>Shinkei Suijaku</span
		>
	</h1>
	<h2 class="p-6 text-lg font-bold text-gray-700">Good luck</h2>
	<div class="flex justify-center">
		<div class="grid max-w-2xl grid-cols-5 place-items-center gap-2">
			{#each grid as card, i}
				{@const isSelected = selected.includes(i)}
				{@const isMatch = matches.includes(card)}
				{@const isSelectedOrMatches = isSelected || isMatch}
				<button
					class="card size-32 rounded-md bg-slate-800 text-6xl"
					class:border-4={isSelected}
					class:border-solid={isSelected}
					class:border-fuchsia-500={isSelected}
					class:flip={isSelectedOrMatches}
					disabled={isSelectedOrMatches}
					onclick={() => selectCard(i)}
				>
					<div class:opacity-40={isMatch} class="back transition-opacity duration-300">
						{card}
					</div>
				</button>
			{/each}
		</div>
	</div>
{/if}

{#if gameState === 'lost'}
	<h1>You lost 🙄</h1>
	<button onclick={() => (gameState = 'playing')}>Play again</button>
{/if}

{#if gameState === 'won'}
	<h1 class="mb-5 text-2xl font-bold text-gray-900 md:text-5xl lg:text-6xl">You won! 🎉</h1>
	<Button class="self-center" onclick={() => (gameState = 'playing')}>Play again</Button>
{/if}

<style>
	.card {
		transition: rotate 0.3s ease-out;
		transform-style: preserve-3d;

		&.flip {
			rotate: y 180deg;
			pointer-events: none;
		}

		& .back {
			position: absolute;
			inset: 0;
			display: grid;
			place-content: center;
			backface-visibility: hidden;
			rotate: y 180deg;
		}
	}
</style>
