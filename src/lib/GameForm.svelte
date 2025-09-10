<script>
	import { onMount } from 'svelte';
	import db from '$lib/firebaseConfig.js';
	import { collection, setDoc, doc, getDoc } from 'firebase/firestore';
	import { goto } from '$app/navigation';

	export let id = null; // if present → edit mode, else add mode

	let game = {
		Name: '',
		Rating: 0,
		Release: '',
		Description: '',
		Screenshots: []
	};

	let newScreenshot = '';
	let isEdit = false;

	// Fetch game if editing
	async function fetchGame(id) {
		try {
			const gameDoc = await getDoc(doc(db, 'Gameslist', id));
			if (gameDoc.exists()) {
				const data = gameDoc.data();
				game = { ...data, Screenshots: data.Screenshots || [] };
				isEdit = true;
			}
		} catch (err) {
			console.error('Error fetching game:', err);
		}
	}

	// Save game (add or update)
	async function saveGame() {
		try {
			const gamesCollection = collection(db, 'Gameslist');
			await setDoc(doc(gamesCollection, game.Name), game);
			console.log(isEdit ? 'Updated game:' : 'Added game:', game.Name);
			goto('/games'); // redirect back to list
		} catch (err) {
			console.error('Error saving game:', err);
		}
	}

	function addScreenshot() {
		if (newScreenshot.trim() !== '') {
			game.Screenshots.push(newScreenshot.trim());
			newScreenshot = '';
		}
	}

	function editScreenshot(index) {
		const newUrl = prompt('Enter new URL:', game.Screenshots[index]);
		if (newUrl) {
			game.Screenshots[index] = newUrl;
		}
	}

	function deleteScreenshot(index) {
		game.Screenshots.splice(index, 1);
	}

	onMount(() => {
		if (id) fetchGame(id);
	});
</script>

<h1>{isEdit ? 'Edit' : 'Add'} Game</h1>

<form on:submit|preventDefault={saveGame} class="submit_form">
	<div class="form-row">
		<label>Game Name:</label>
		<input bind:value={game.Name} required />

		<label>Score:</label>
		<input bind:value={game.Rating} type="number" step="0.1" min="0" required />

		<label>Date:</label>
		<input bind:value={game.Release} type="date" required />
	</div>

	<div class="form-row">
		<label>Description:</label>
		<textarea bind:value={game.Description} required></textarea>
	</div>

	<div class="form-row">
		<label>Screenshots:</label>
		<input bind:value={newScreenshot} placeholder="Enter screenshot URL" />
		<button type="button" on:click={addScreenshot}>Add Screenshot</button>
	</div>

	{#if game.Screenshots.length > 0}
		<table class="screenshots">
			<tbody>
				{#each game.Screenshots as screenshot, index}
					<tr>
						<td
							style="max-width: 300px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis;"
						>
							{screenshot}
						</td>
						<td><img src={screenshot} alt="Screenshot" width="100" /></td>
						<td><button type="button" on:click={() => editScreenshot(index)}>✏️</button></td>
						<td><button type="button" on:click={() => deleteScreenshot(index)}>🗑️</button></td>
					</tr>
				{/each}
			</tbody>
		</table>
	{/if}

	<div class="form-row">
		<button type="submit">Save</button>
	</div>
</form>
