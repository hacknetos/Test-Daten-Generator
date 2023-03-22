<script lang="ts" async>
	import { readTextFile, BaseDirectory } from '@tauri-apps/api/fs';
	import { writeText, readText } from '@tauri-apps/api/clipboard';
	import { search } from '$lib/store';
	let content: string = '';
	const getText = async () => {
		content = await readTextFile('Data.JSON', { dir: BaseDirectory.Download });
		content = await content.slice(2, content.length - 2);
	};
	getText();
	let searchValue = '';
	search.subscribe((value) => {
		searchValue = value;
	});


</script>

<h1 class="Pagename">Read Test Data</h1>
<div class="Text">
	<!-- {content.split('},{')} -->
	{#if content.length > 0}
		{#each content.split('},{') as tmp}
			{#if tmp.indexOf(searchValue) != -1}
				<div class="DataOBJ" on:click={ async(e)=>{await writeText('{'+tmp.toString()+'}')
				}}>
					{#each tmp.split(',') as tmp2}
						<p>{tmp2}</p>
					{/each}
				</div>
			{/if}
		{/each}
	{/if}
</div>

<style>
	/* :root {
		--cardwidth: 266px;
	} */
	.Pagename {
		text-align: center;
		/* break-after: "}"; */
	}
	.DataOBJ {
		margin-bottom: 1.5%;
		border: 1px solid rgb(87, 86, 86);
		width: 344px;
		border-radius: 5px;
		padding-left: 10px;
		text-align: justify;
		overflow-y: hidden;
		transition: all 0.5s ease;
	}
	.DataOBJ:hover {
		box-shadow: 6px 4px 10px rgb(88, 84, 84);
	}
	.Text {
		overflow-y: hidden;
		display: flex;
		flex-direction: row;
		/* flex-flow: row; */
		justify-content: space-evenly;
		flex-wrap: wrap;
	}
	ul {
		list-style: none;
	}
	input {
	}
</style>
