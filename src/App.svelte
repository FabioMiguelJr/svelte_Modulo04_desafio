<script>
	import ListaAudios from "./ListaAudios.svelte";

	let audios = [];
	let audiosEl = "Gravação_";
	let audios1El = "Áudio_";
	let estado_grv = "Aguardando";

	let chunks = [];
	navigator.mediaDevices
		.getUserMedia({
			audio: true,
			video: false,
		})
		.then((stream) => {
			const btnGravar = document.getElementById("gravar");
			const btnParar = document.getElementById("parar");
			const estado = document.getElementById("estado");
			btnParar.disabled = true;
			let mediaRecorder = new MediaRecorder(stream);

			btnGravar.onclick = () => {
				mediaRecorder.start();
				console.log(mediaRecorder.state);
				console.log("recorder started");
				btnGravar.style.background = "red";
				btnGravar.style.color = "black";
				btnParar.disabled = false;
				estado.style.color = "red";
				estado_grv = "Gravando";
			};

			btnParar.onclick = () => {
				mediaRecorder.stop();
				console.log(mediaRecorder.state);
				console.log("recorder stopped");
				btnGravar.style.background = "";
				btnGravar.style.color = "";
				btnParar.disabled = true;
				estado.style.color = "black";
				estado_grv = "Aguardando";
			};

			mediaRecorder.ondataavailable = function (e) {
				chunks.push(e.data);
			};

			mediaRecorder.onstop = function (e) {
				const blob = new Blob(chunks, { type: "audio/ogg; codecs=opus" });
				chunks = [];

				const audioURL = URL.createObjectURL(blob);
				adicionarAudios(audioURL);
				estado_grv = "Aguardando";
			};

			function adicionarAudios(audioURL) {
				const index = audios.length + 1;
				console.log(index);
				audios = [...audios, { audio: audioURL, descricao: audiosEl + index }];
				console.log(audios);
			}
		});

	function removerAudios(audio) {
		audios = audios.filter((i) => i !== audio);
		console.log(audios);
	}
</script>

<main>
	<h1>Gravador de áudio</h1>
	<div class="gravador">
		<button id="gravar" class="btn">Gravar</button>
		<button id="parar" class="btn">Parar</button>
		<p id="estado" class="par">{estado_grv}</p>
	</div>
	{#if audios.length === 0}
		<div class="gravador">A lista está vazia</div>
	{:else}
		{#each audios as item, i}
			<ListaAudios audio={item.audio} descricao={item.descricao} on:itemremovido={() => removerAudios(item)} />
		{/each}
	{/if}
</main>

<style>
	.gravador,
	h1 {
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.btn {
		margin-right: 10px;
	}

	.btn,
	.par {
		font-family: "Lucida Sans", "Lucida Sans Regular", "Lucida Grande", "Lucida Sans Unicode", Geneva, Verdana, sans-serif;
	}
</style>
