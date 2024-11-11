<script lang="ts">
	import { emojis } from './emojis';

	type GameState = 'start' | 'playing' | 'pause' | 'won' | 'lost';
	let state = $state<GameState>('start');
	const size = 20;
	const grid = initGrid();

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

{#if state === 'start'}
	<h1 class="text-2xl font-bold text-gray-900 md:text-5xl lg:text-6xl">
		Welcome to <span
			class="bg-gradient-to-r from-sky-400 to-emerald-600 bg-clip-text text-transparent"
			>Shinkei Suijaku</span
		>
	</h1>
	<button aria-label="play-game" onclick={() => (state = 'playing')}>Play</button>
{/if}

{#if state === 'playing'}
	<h1 class="text-2xl font-bold text-gray-900 md:text-5xl lg:text-6xl">
		Playing <span class="bg-gradient-to-r from-sky-400 to-emerald-600 bg-clip-text text-transparent"
			>Shinkei Suijaku</span
		>
	</h1>
	<h2 class="p-6 text-lg font-bold text-gray-700">Good luck</h2>
	<div class="flex justify-center">
		<div class="grid max-w-2xl grid-cols-5 place-items-center gap-2">
			{#each grid as card, i}
				<button class="size-32 rounded-md bg-slate-800 text-6xl">
					<div>{card}</div>
				</button>
			{/each}
		</div>
	</div>
{/if}

{#if state === 'lost'}
	<h1>You lost 🙄</h1>
	<button onclick={() => (state = 'playing')}>Play again</button>
{/if}

{#if state === 'won'}
	<h1>You won! 🎉</h1>
	<button onclick={() => (state = 'playing')}>Play again</button>
{/if}

<style>
	.cards {
		display: grid;
		grid-template-columns: repeat(4, 1fr);
		gap: 0.5rem;
	}
</style>
