<script>
	import { auth } from '$lib/firebaseConfig';
	import { signInWithEmailAndPassword } from "firebase/auth";
	import { goto } from '$app/navigation';
	import { FormInput, Button } from 'components';
	import { user } from '$lib/stores/userStore.js'; // Import the store

	let email = $state('');
	let password = $state('');
	let error = $state('');
	let loading = $state(false);

	async function login() {
		loading = true;
		error = "";
		try {
			const userCredential = await signInWithEmailAndPassword(auth, email, password);
			console.log("User signed in:", userCredential.user);
			user.set(userCredential.user); // Update the store
			goto('/games')
		} catch (err) {
			error = err.message;
			console.error(err);
			console.log("Login failed:", err);
		} finally {
			loading = false;
		}
	}
</script>

<div class="myform">
	<FormInput name="Email" bind:value={email} type="email" labelText="Email" required />
	<FormInput name="Password" bind:value={password} type="text" labelText="Password" />
	<Button type="button" text={loading ? "Logging in..." : "Submit"} onclick={login} />
</div>

{#if error}
	<p style="color: red">{error}</p>
{/if}

<style>
	.myform {
		width: min(400px , 90%);
		margin: 2rem auto;
		padding: 1rem;
		border: 1px solid #ccc;
		border-radius: 8px;
		box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
		background-color: #f9f9f9;
	}
</style>