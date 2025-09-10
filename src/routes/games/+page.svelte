<script>
  import { onMount } from "svelte";
  import db from "$lib/firebaseConfig.js";
  import { collection, getDocs, deleteDoc, doc } from "firebase/firestore";
  import { goto } from "$app/navigation";

  let games = [];

  async function fetchGames() {
    try {
      const querySnapshot = await getDocs(collection(db, "Gameslist"));
      games = querySnapshot.docs.map((docSnap) => ({
        id: docSnap.id,
        ...docSnap.data()
      }));
    } catch (err) {
      console.error("Error fetching games:", err);
    }
  }

  function addGame() {
    goto("/games/add");
  }

  function editGame(id) {
    goto(`/games/${id}`);
  }

  async function deleteGame(id) {
    if (confirm("Are you sure you want to delete this game?")) {
      try {
        await deleteDoc(doc(db, "Gameslist", id));
        games = games.filter((game) => game.id !== id);
        console.log("Deleted game:", id);
      } catch (err) {
        console.error("Error deleting game:", err);
      }
    }
  }

  onMount(fetchGames);
</script>

<h1>Games List ({games.length})</h1>

<button on:click={addGame}>➕ Add Game</button>

{#if games.length === 0}
  <p>No games found.</p>
{:else}
  <table border="1" cellpadding="10">
    <thead>
      <tr>
        <th>Name</th>
        <th>Rating</th>
        <th>Release</th>
        <th>Description</th>
        <th>Screenshots</th>
        <th>Actions</th>
      </tr>
    </thead>
    <tbody>
      {#each games as game}
        <tr>
          <td>{game.Name}</td>
          <td>{game.Rating}</td>
          <td>{game.Release}</td>
          <td>{game.Description}</td>
          <td>
            {#if game.Screenshots?.length > 0}
              <img src={game.Screenshots[0]} alt="Screenshot" width="100" />
            {:else}
              <em>No screenshot</em>
            {/if}
          </td>
          <td>
            <button on:click={() => editGame(game.id)}>✏️ Edit</button>
            <button on:click={() => deleteGame(game.id)}>🗑️ Delete</button>
          </td>
        </tr>
      {/each}
    </tbody>
  </table>
{/if}

<style>
		.submit_form {
		padding: 10px;
		border-radius: 5px;
		border: 1px solid var(--color1);
		width: fit-content;
		margin: 0 auto;
		.form-row {
			padding: 10px;
		}
		label {
			display: inline-block;
			margin: 0 10px 0 20px;
		}
		input {
			margin: 0 20px 0 10px;
			padding: 5px;
			border-radius: 5px;
			border: 1px solid var(--color1);
		}
		textarea {
			margin: 0 20px 0 10px;
			border-radius: 5px;
			border: 1px solid var(--color1);
			width: 80%;
			height: 100px;
		}
		button {
			margin: 10px;
			padding: 5px 10px;
			border-radius: 5px;
			border: 1px solid var(--color1);
			background-color: var(--color1);
			color: var(--color3);
			font-size: larger;
		}
		table.screenshots {
			margin: 10px auto;
			border-collapse: collapse;
			th,
			td {
				border: 1px solid var(--color1);
				padding: 5px;
			}
			th {
				background-color: var(--color1);
				color: var(--color3);
			}
			td {
				img {
					width: 100px;
					height: auto;
				}
			}
		}
	}
</style>