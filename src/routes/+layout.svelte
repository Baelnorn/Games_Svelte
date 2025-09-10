<script lang="ts">
	import '../app.css';
	import favicon from '$lib/assets/favicon.svg';
	import { user } from '$lib/stores/userStore.js';

	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
</svelte:head>

<header>
	<h1>My App {#if $user} Welcome {$user.email} {/if}</h1>
	<nav>
		<ul>
			<li><a href="/">Home</a></li>
			<li><a href="/backlog">Backlog</a></li>
		{#if $user}
			<li><a href="/games">Games</a></li>
		{:else}
			<li><a href="/form">Log in</a></li>
		{/if}
		</ul>
	</nav>
</header>
<main>
	{@render children?.()}   <!-- this swaps depending on route -->
</main>
<footer>
	<p>© 2025 Game list</p>
</footer>


<style>
	header{
		nav{
			ul{
				list-style: none;
				display: flex;
				gap: 1rem;
				padding: 0;
				justify-content: center;
			}

			a{
				text-decoration: none;
				color: var(--color-accent);
				
			}
			a:hover{
				text-decoration: underline;
			}	
		}
	}
	footer{
		margin-top: 2rem;
	}
</style>