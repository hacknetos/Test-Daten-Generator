<script lang="ts">
	import { search } from '$lib/store';
	let searchValue = '';
	const SetValue = () => {
		search.set(searchValue);
	};

	import './App.css';
</script>

<nav>
	<a href="/">Create</a>
	<a href="/show">Show</a>
	<input
		type="search"
		bind:value={searchValue}
		on:input={SetValue}
		placeholder="search Data 🔎"
	/>
</nav>
<div>
	<slot />
</div>

<style>
	nav {
		background-color: rgba(63, 63, 63, 0.726);
		display: flex;
		justify-content: space-evenly;
        
		align-items: center;
		padding: 1rem;
		border-radius: 5px;
        position: sticky;
        top: 1rem;
        margin: 0px auto;
        width: 90vw;
        
	}
	a {
		color: whitesmoke;
		text-decoration: none;
		transition: all 0.2s ease;
		position: relative;

	}
	a:hover {
		color: aqua;
	}
    input {
        border: transparent;
        background-color: transparent;
        border-bottom: 1px white solid;
        color: white;
        
    }
    input:focus {
        outline: transparent;
    }
    ::placeholder {
        color: rgba(255, 255, 255, 0.493);
    }
    
</style>
