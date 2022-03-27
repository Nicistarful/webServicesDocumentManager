<script>
	import axios from 'axios'
	import ListElement from './ListElement.svelte'

	let filenames = ['']
	let files = []
	let filteredFiles = ['']
	let uploadButton
	let searchString = ''

	const searchChanged = () => {
		filteredFiles = filenames.filter(file =>
			file.toLowerCase().includes(searchString.toLowerCase())
		)
	}

	async function getFilenames() {
		const response = await axios.get('http://localhost/fetchFilenames')
		const data = response.data
		filenames = data
	}

	async function downloadFile(name) {
		const url = await axios.get(
			`http://localhost/downloadFile?name=${name}`
		)
		console.log(url.data)
		window.open(url.data)
	}

	async function uploadFile() {
		let formData = new FormData()
		formData.append('file', files[0])
		axios.post('http://localhost/uploadFile', formData, {
			headers: {
				'Content-Type': 'multipart/form-data',
			},
		})
		axios.post(
			`http://localhost/uploadFileIndex?path=${files[0].path}&filename=${files[0].name}`
		)
		uploadButton.value = ''

		getFilenames()
	}

	getFilenames()
</script>

<main class="w-full">
	<nav class="h-12 bg-slate-500 flex justify-center p-2 gap-8">
		<input
			type="text"
			class="h-full w-96 rounded-full outline-none p-2"
			placeholder="Search..."
			bind:value={searchString}
			on:input={searchChanged}
		/>
		<input
			type="file"
			bind:files
			class="text-white"
			bind:this={uploadButton}
		/>
		<button on:click={uploadFile} class="bg-gray-100 px-4">Upload</button>
		<button on:click={getFilenames} class="bg-gray-100 px-4">Refresh</button
		>
	</nav>
	<div class="py-8 px-16">
		<ul>
			{#if searchString == ''}
				{#each filenames as filename}
					<li>
						<ListElement
							name={filename}
							download={() => downloadFile(filename)}
						/>
					</li>
				{/each}
			{:else}
				{#each filteredFiles as file}
					<li>
						<ListElement
							name={file}
							download={() => downloadFile(file)}
						/>
					</li>
				{/each}
			{/if}
		</ul>
	</div>
</main>
