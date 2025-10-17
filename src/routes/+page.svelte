<script lang="ts">
	import { onMount, onDestroy } from 'svelte';
	import { collection, getDocs } from 'firebase/firestore';
	import db from '$lib/firebaseConfig.js';
	import { fade } from 'svelte/transition';

	// Type for Game
	type Game = {
		id: string;
		Name: string;
		Rating?: number;
		Release?: string;
		Description?: string;
		Screenshots?: string[];
	};

	let games: Game[] = [];

	// Store current indices for each game
	let currentIndices: Record<string, number> = {};

	// Store next indices for fade transition
	let nextIndices: Record<string, number> = {};

	// Store timeouts for cleanup
	const timeouts: Record<string, ReturnType<typeof setTimeout>> = {};

	const min = 5000;
	const max = 7000;
	const fadeDuration = 800;

	async function fetchGames() {
		try {
			const snapshot = await getDocs(collection(db, 'Gameslist'));
			games = snapshot.docs.map((doc) => ({ id: doc.id, ...doc.data() })) as Game[];

			// Initialize indices and start crossfade
			games.forEach((game) => {
				currentIndices[game.id] = 0;
				nextIndices[game.id] = 1 % (game.Screenshots?.length || 1);
				startCrossfade(game.id);
			});
		} catch (err) {
			console.error(err);
		}
	}

	function startCrossfade(gameId: string) {
		function scheduleNext() {
			const intervalTime = Math.floor(Math.random() * (max - min + 1)) + min;

			timeouts[gameId] = setTimeout(() => {
				const game = games.find((g) => g.id === gameId);
				if (game && game.Screenshots && game.Screenshots.length > 1) {
					// Update next index
					nextIndices[gameId] = (currentIndices[gameId] + 1) % game.Screenshots.length;

					// After fade duration, swap current index
					setTimeout(() => {
						currentIndices[gameId] = nextIndices[gameId];
					}, fadeDuration); 
				}

				scheduleNext();
			}, intervalTime);
		}

		scheduleNext();
	}

	// Sorting toggles
	let nameAsc = true;
	let ratingAsc = false;

	function sortByName() {
		games = [...games].sort((a, b) =>
			nameAsc ? a.Name.localeCompare(b.Name) : b.Name.localeCompare(a.Name)
		);
		nameAsc = !nameAsc;
	}

	function sortByRating() {
		games = [...games].sort((a, b) =>
			ratingAsc ? (a.Rating || 0) - (b.Rating || 0) : (b.Rating || 0) - (a.Rating || 0)
		);
		ratingAsc = !ratingAsc;
	}

	onMount(fetchGames);

	onDestroy(() => {
		Object.values(timeouts).forEach(clearTimeout);
	});
</script>

<h1>Games List ({games.length} games)</h1>

<button class="sort-button" on:click={sortByName}>
	Sort by Name {nameAsc ? '▲' : '▼'}
</button>

<button class="sort-button" on:click={sortByRating}>
	Sort by Rating {ratingAsc ? '▲' : '▼'}
</button>

<div class="game-items">
	{#each games as game (game.id)}
		<div class="game">
			<div class="head">
				<div class="name">{game.Name}</div>
				<div class="rating">{game.Rating}</div>
			</div>

			<div class="body">
				<div>
					<h5>Date: {game.Release}</h5>
					<p>Description: {game.Description}</p>
				</div>
				<div>
					{#if game.Screenshots && game.Screenshots.length > 0}
						<div class="screenshot-container">
							{#key currentIndices[game.id]}
								<img
									src={game.Screenshots[currentIndices[game.id]]}
									alt="Screenshot"
									in:fade={{ duration: fadeDuration }}
									out:fade={{ duration: fadeDuration }}
								/>
							{/key}
						</div>
					{:else}
						<p>No screenshot available</p>
					{/if}
				</div>
			</div>
		</div>
	{/each}
</div>

<style>
	.sort-button {
		font-size: 1em;
		cursor: pointer;
		background-color: var(--color-secondary);
		color: var(--color-accent);
		border: none;
	}

	.game-items > div {
		margin: 20px 0;
		text-align: left;
		border: 1px solid #ccc;
		border-radius: 8px;
		box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
		background-color: #f9f9f9;
		color: var(--color0);
	}

	.screenshot-container {
		position: relative;
		width: 200px;
		height: 120px;
		overflow: hidden;
		margin: 20px 10px;
	}

	.screenshot-container img {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		object-fit: cover;
	}

	.head {
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 10px;
		background-color: var(--color-accent3);
	}

	.head .name {
		font-size: 1.2em;
		font-weight: bold;
	}

	.head .rating {
		min-width: 50px;
		height: 50px;
		display: flex;
		justify-content: center;
		align-items: center;
		text-align: center;
		background-color: var(--color-accent2);
		font-weight: bold;
		font-size: 1.5em;
	}

	.body {
		display: flex;
		justify-content: space-between;
		color: var(--color-accent0-2);
	}

	.game-items > div p,
	h5 {
		margin: 0;
		padding: 10px;
	}
</style>
