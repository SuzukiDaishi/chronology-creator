<script>
	import marked from 'marked'
	import { each } from 'svelte/internal'
	export let title = ''
	export let description = ''
	export let editorToggle = false
	export let chronology = []
	export let year
	export let text
	export function toMarkdoun(str) {
		if (!str) return ''
		return marked(str)
	}
	export function toWareki(year) {
		return (new Date(year, 0, 0)).toLocaleDateString('ja-JP-u-ca-japanese', {year:'numeric'})
	}
	export function pushChronology(year, text) {
		chronology.push({ year, text })
		chronology = chronology
	}
	export function removeChronology(idx) {
		chronology.splice(idx, 1)
		chronology = chronology
	}
</script>

<main>
	<h1>年表作成</h1>
	<div class="box" contenteditable="true" on:click={() => editorToggle = true}>
		インポートJSON: <input type="file" on:change={(e) => {
			const reader = new FileReader()
			reader.readAsText(e.target.files[0])
			reader.onload = (e) => {
				const obj = JSON.parse(e.target.result)
				title = obj.title
				description = obj.description
				chronology = obj.chronology
			}
		}}><br>
		タイトル: <input type="text" bind:value={title}><br>
		<textarea placeholder="Markdounで摘要を書いてね" bind:value={description}></textarea>
		<h1>{title}</h1>
	 	{@html toMarkdoun(description)}
	</div>
	<div class="box">
		<div>
			年代など: <input type="number" bind:value={year}><br>
			主要事項: <input type="text" bind:value={text}><br>
			<button on:click={() => { 
				pushChronology(year, text) 
				year = ''
				text = ''
			}}>登録</button>
		</div>
		<div>
			<table border="1">
				<tr>
					<th>年代など</th>
					<th>西暦</th>
					<th>主要事項</th>
					<th>削除ボタン</th>
				</tr>
				{#each chronology as c, i }
					<tr>
						<td>{toWareki(c.year)}</td>
						<td>{c.year}</td>
						<td>{@html toMarkdoun(c.text)}</td>
						<td>
							<button on:click={() => { removeChronology(i) }}>削除</button>	
						</td>
					</tr>
				{/each}
			</table>
		</div>
	</div>
	<div class="box">
		<button on:click={() => {
			const fileName = `${title}.json`
			const content = JSON.stringify({
				title,
				description,
				chronology,
			}, null, 2)

			const blob = new Blob( [ content ], { 'type': 'text/plain' } )
			const a = document.createElement('a')
			a.download = fileName
			a.target   = '_blank'
			if (window.navigator.msSaveBlob) {
				window.navigator.msSaveBlob(blob, fileName)
			} else if (window.URL && window.URL.createObjectURL) {
				a.href = window.URL.createObjectURL(blob)
				document.body.appendChild(a)
				a.click()
				document.body.removeChild(a);
			} else if (window.webkitURL && window.webkitURL.createObject) {
				a.href = window.webkitURL.createObjectURL(blob)
				a.click()
			} else {
				window.open('data:text/plain;base64,' + window.Base64.encode(content), '_blank')
			}
		}}>エクスポートJSON</button>
		<button on:click={() => {　alert('近日実装')　}}>エクスポートCSV</button>
	</div>
</main>

<style>
.box {
	width: 100%;
	margin-bottom: 20px;
	padding: 3px;
	box-shadow: 0px 0px 16px -6px rgba(0,0,0,0.6);
}
textarea {
	width: 80%;
	height: 100px;
	resize: none;
}
table {
	width: 80%;
}
</style>