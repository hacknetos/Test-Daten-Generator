<script lang="ts">
	import {
		isPermissionGranted,
		requestPermission,
		sendNotification
	} from '@tauri-apps/api/notification';
	import { message } from '@tauri-apps/api/dialog';
	import { rows } from '../routes/stores';
	import { writeTextFile, BaseDirectory } from '@tauri-apps/api/fs';
	//@ts-ignore
	import { v4 as uuidv4 } from 'uuid';
	import { faker } from '@faker-js/faker';
	let anzahldataseztz: number = 500;

	type row = {
		name: string;
		Type: string;
		Values: any;
		options: string | undefined;
	};
	let answer: number;
	let myrows: row[] = [];

	rows.subscribe((value) => {
		answer = value;
		myrows = [];
		for (let i = 0; i < answer; i++) {
			myrows[i] = { name: '', Type: '', Values: '', options: '' };
		}
	});
	const buildDatasetzt = async (setzt: number) => {
		let element = '[';
		for (let i = 0; i < setzt; i++) {
			element += '{';
			let forname = '';
			let nachname = '';
			await myrows.forEach((row) => {
				switch (row.Type) {
					case 'UUID':
						row.Values = uuidv4();
						break;
					case 'ID':
						row.Values = i + 1;
						break;
					case 'Firstname':
						if (forname == '') {
							forname = faker.name.firstName();
						}
						row.Values = forname;
						break;
					case 'Lastname':
						if (nachname == '') {
							nachname = faker.name.lastName();
						}
						row.Values = nachname;
						break;
					case 'Email':
						if (forname == '') {
							forname = faker.name.firstName();
						}
						if (nachname == '') {
							nachname = faker.name.lastName();
						}
						row.Values = faker.internet.email(forname, nachname);
						break;
					case 'username':
						if (forname == '') {
							forname = faker.name.firstName();
						}
						if (nachname == '') {
							nachname = faker.name.lastName();
						}
						row.Values = faker.internet.userName(forname, nachname);
						break;
					case 'Passwort':
						row.Values = faker.internet.password();
						break;
					case 'Status':
						switch (row.options) {
							case 'Online':
								switch (Math.floor(Math.random() * (4 - 0) + 0)) {
									case 0:
										row.Values = 'online';
										break;
									case 1:
										row.Values = 'offline';
										break;
									case 2:
										row.Values = 'Abwesent';
										break;
									case 3:
										row.Values = 'Nicht stören';
										break;

									default:
										break;
								}

								break;
							case 'verfückbarkeit':
								switch (Math.floor(Math.random() * (4 - 0) + 0)) {
									case 0:
										row.Values = 'Verfügbar';
										break;
									case 1:
										row.Values = 'nicht Verfügbar';
										break;
									case 2:
										row.Values = 'balt wieder Verfügbar';
										break;
									case 3:
										row.Values = 'nie wieder Verfügbar';
										break;
									default:
										break;
								}

								break;
							case 'support':
								switch (Math.floor(Math.random() * (4 - 0) + 0)) {
									case 0:
										row.Values = 'on Hold';
										break;
									case 1:
										row.Values = 'in Bearbeitung';
										break;
									case 2:
										row.Values = 'close';
										break;
									case 3:
										row.Values = 'Undwonkt';
										break;
									default:
										break;
								}
								break;

							default:
								break;
						}
						break;
					case 'Anstellung':
						///TODO Anstaellung
						switch (Math.floor(Math.random() * (8 - 0) + 0)) {
							case 0:
								row.Values = 'CEO';
								break;
							case 1:
								row.Values = 'Support';

								break;
							case 2:
								row.Values = 'Developer';

								break;
							case 3:
								row.Values = 'Forschung';

								break;
							case 4:
								row.Values = 'Sicherheit';

								break;
							case 5:
								row.Values = 'Lager';

								break;
							case 6:
								row.Values = 'Lieferung';

								break;
							case 7:
								row.Values = 'Buchhaltung';

								break;
							default:
								break;
						}
						break;
					case 'Int':
						row.Values = Math.floor(Math.random() * (10000 - 0) + 0);
						break;
					case 'Text':
						row.Values = faker.lorem.lines(1);
						break;
					case 'Enum':
						//@ts-ignore
						let tmp = row.options.split(',');
						row.Values = tmp[Math.floor(Math.random() * (tmp.length - 0) + 0)];
						break;
					case 'Date':
						row.Values = faker.date.between('1999-01-01T00:00:00.000Z', '2077-01-01T00:00:00.000Z');

						break;
					case 'Floute':
						row.Values = Math.random();

						break;
					default:
						break;
				}
				console.log('Typeof row.Values ', typeof row.Values);

				if (typeof row.Values == 'number') {
					element += `"${row.name}":${row.Values},`;
				} else {
					element += `"${row.name}":"${row.Values}",`;
				}
			});
			element = element.slice(0, element.length - 1);
			element += '},';
			forname = '';
			nachname = '';
		}
		element = element.slice(0, element.length - 1);
		element += ']';

		console.log(element);

		await writeTextFile('Data.JSON', element, { dir: BaseDirectory.Download });
		let permissionGranted = await isPermissionGranted();
		if (!permissionGranted) {
			const permission = await requestPermission();
			permissionGranted = permission === 'granted';
		}
		if (permissionGranted) {
			sendNotification('Finish the Process');
		}
		const MYmessage = await message('Program is is Finish');
	};
</script>

<div>
	<table>
		<thead>
			<tr>
				<td> Name </td>
				<td> Type </td>
				<td> Values </td>
			</tr>
		</thead>
		<tbody>
			{#each myrows as row}
				<tr>
					<td><input type="text" bind:value={row.name} placeholder="Name" /></td>
					<td
						><select bind:value={row.Type}>
							<option value="UUID">UUID</option>
							<option value="ID">ID</option>

							<option value="Firstname">Firstname</option>
							<option value="Lastname">Lastname</option>

							<option value="Email">Email</option>
							<option value="username">username</option>
							<option value="Passwort">Passwort</option>
							<option value="Status">Status</option>
							<option value="Anstellung">Anstellung</option>

							<option value="Int">Int</option>
							<option value="Text">Text</option>
							<option value="Enum">Enum</option>
							<option value="Date">Date</option>
							<option value="Floute">Floute</option>
						</select></td
					>
					<td
						>{#if row.Type == 'Status'}
							<select class="valuesin" bind:value={row.options}>
								<option value="Online">Online</option>
								<option value="verfückbarkeit">verfückbarkeit</option>
								<option value="support">support</option>
							</select>
						{:else if row.Type == 'Enum'}
							<input
								class="valuesin"
								type="text"
								bind:value={row.options}
								placeholder="Online,Offline,Abwesend"
							/>
						{:else}
							<input
								class="valuesin"
								disabled
								placeholder="Must Enum or Status"
								value="Must Enum or Status"
							/>
						{/if}</td
					>
				</tr>
			{/each}
		</tbody>
	</table>
	<div class="Save">
		<input type="number" bind:value={anzahldataseztz} />
		<button
			on:click={async () => {
				await buildDatasetzt(anzahldataseztz);
			}}>Save</button
		>
	</div>
</div>

<style>
	table {
		width: 100vw;
		margin: 10px 0px;
	}
	tr {
		display: flex;
		justify-content: space-evenly;
	}
	td {
		text-align: center;
	}
	.valuesin {
		width: 10rem;
		margin: 0px;
	}
	select.valuesin {
		width: 10.5rem;
	}
	.Save {
		display: flex;
		flex-direction: row;
		justify-content: center;
	}
	.Save input {
		margin-right: 5px;
		border: transparent;
		background-color: transparent;
		border-bottom: 1px solid white;
		color: white;
	}
	.Save input:focus {
		outline: transparent;
	}
	.Save button {
		margin-left: 5px;
	}
</style>
