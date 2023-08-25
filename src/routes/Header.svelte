<script>
	import { page } from '$app/stores';
</script>




<header>
	<div class="top-section">
		<h1>Md Shamim Ahmed	</h1>
		<h2>ID - 2263466  </h2>
		<h3>Hasselt University </h3>
	</div>
	
	
<nav>
  <ul>
    <li aria-current={$page.url.pathname === '/visual' ? 'page' : undefined}>
      <a href="/visual" target="_self">HOME</a>
    </li>
    <li aria-current={$page.url.pathname === '/GeneData' ? 'page' : undefined}>
      <a href="/GeneData" target="_self">Gene Overview</a>
    </li>
    <li aria-current={$page.url.pathname === '/interactions' ? 'page' : undefined}>
      <a href="/interactions" target="_self">Interactions Overview</a>
    </li>
  </ul>
</nav>


</header>

<style>
	header {
		display: flex;
		flex-direction: column;
		align-items: center;
		background-color: var(--color-theme-1); /* Define your theme color */
		padding: 1rem;
		color: white;
	}

	.top-section {
		text-align: center;
		margin-bottom: 1rem;
	}

	nav {
		display: flex;
		justify-content: center;
		background-color: var(--background);
		padding: 0.5rem;
	}

	/* svg {
		width: 1em;
		height: 2em;
		display: block;
	} */

	/* path {
		fill: var(--background);
	} */

	ul {
		list-style: none;
		margin: 0;
		padding: 0;
		display: flex;
	}

	li {
		margin: 0 0.5rem;
	}

	li[aria-current='page']::before {
		--size: 6px;
		content: '';
		width: 0;
		height: 0;
		position: absolute;
		top: 0;
		left: calc(50% - var(--size));
		border: var(--size) solid transparent;
		border-top: var(--size) solid white;
	}

	nav a {
		color: white;
		text-decoration: none;
		font-weight: 600;
		transition: color 0.2s ease-in-out;
	}

	a:hover {
		color: var(--color-theme-2); /* Define another color for hover */
	}
</style>
