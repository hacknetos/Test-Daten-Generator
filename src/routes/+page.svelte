<script lang="ts">
	import Buildtable from '$lib/buildtable.svelte';
	import { rows } from './stores';
	let values: number = 4;
</script>

<h1 class="Pagename">Create Test Data</h1>
<p class="Pagename">Anzahl der Spalten</p>
<div class="SpatenSelect">
	<input bind:value={values} type="number" />
	<button
		on:click={() => {
			rows.set(values);
		}}>Ok</button
	>
</div>
<Buildtable />

<style>
	.Pagename {
		text-align: center;
	}
    .SpatenSelect {
        display: flex;
        justify-content: center;
        align-items: center;
        align-content: center;
    }
    .SpatenSelect input {
		margin-right: 5px;
	}
	.SpatenSelect button {
		margin-left: 5px;
	}
</style>
