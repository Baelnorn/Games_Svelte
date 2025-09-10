<script lang="ts">
	import favicon from '$lib/assets/favicon.svg';
	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
</svelte:head>

<header>
	<h1>My App</h1>
	<nav>
		<ul>
			<li><a href="/">Home</a></li>
			<li><a href="/games">Games</a></li>
			<li><a href="/form">Form</a></li>
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