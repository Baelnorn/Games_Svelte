<script>

	import { onMount } from 'svelte';
	import { collection, getDocs, doc, deleteDoc } from 'firebase/firestore';
	import db from '$lib/firebaseConfig.js';

	let games = [];
	let screenshotIndices = {};

	// Fetch games
	async function fetchGames() {
		try {
			const gamesCollection = collection(db, 'Gameslist');
			const snapshot = await getDocs(gamesCollection);
			games = snapshot.docs.map((doc) => ({ id: doc.id, ...doc.data() }));

			// Initialize screenshot indices
			games.forEach((game) => {
				screenshotIndices[game.id] = 0;
			});

			// 🔥 force reactivity after init
			screenshotIndices = { ...screenshotIndices };
		} catch (error) {
			console.error('Error fetching games:', error);
		}
	}

  // Toggle flags
  let nameAsc = true;    // true = ascending, false = descending
  let ratingAsc = false;

  function sortByName() {
    games = [...games].sort((a, b) => 
      nameAsc
        ? a.Name.localeCompare(b.Name)
        : b.Name.localeCompare(a.Name)
    );
    nameAsc = !nameAsc;  // toggle for next click
  }

  function sortByRating() {
    games = [...games].sort((a, b) =>
      ratingAsc
        ? a.Rating - b.Rating
        : b.Rating - a.Rating
    );
    ratingAsc = !ratingAsc; // toggle for next click
  }
	// Lifecycle: on mount
	onMount(() => {
		fetchGames();
		const interval = setInterval(() => {
			let changed = false;
			games.forEach((game) => {
				if (game.Screenshots && game.Screenshots.length > 1) {
					screenshotIndices[game.id] =
						(screenshotIndices[game.id] + 1) % game.Screenshots.length;
					changed = true;
				}
			});

			// 🔥 trigger Svelte reactivity
			if (changed) {
				screenshotIndices = { ...screenshotIndices };
			}
		}, 5000);

		// cleanup when component is destroyed
		return () => clearInterval(interval);
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
	{#each games as game, index (game.Name)}
	
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
						<img
							src={game.Screenshots[screenshotIndices[game.id]]}
							alt="Screenshot"
						/>
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
		img {
			width: 200px;
			height: auto;
			margin: 20px 10px;
		}
		.head {
			display: flex;
			justify-content: space-between;
			align-items: center;
			padding: 10px;
			background-color: var(--color-accent3);
			.name {
				font-size: 1.2em;
				font-weight: bold;
			}
			.rating {
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
		}
		.body {
			display: flex;
			justify-content: space-between;
			color: var(--color-accent0-2);
		}
	}

	.game-items > div p {
		margin: 0;
		padding: 10px;
	}
</style>
