<script>
	import { createEventDispatcher } from "svelte";
	const dispatch = createEventDispatcher();
	export let descricao;
	export let audio;
</script>

<div class="gravador" id="divAudio">
	{descricao}
	<audio controls src={audio} />
	<button on:click={() => dispatch("itemremovido")}>Excluir</button>
</div>

<style>
	.gravador {
		display: flex;
		align-items: center;
		justify-content: center;
	}
</style>
