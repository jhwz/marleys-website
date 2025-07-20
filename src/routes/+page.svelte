<script lang="ts">
	import { Confetti } from 'svelte-confetti';

	type Props = {};
	let {}: Props = $props();

	let showConfetti = $state(0);
</script>

<h1>Welcome to Marley's Website!</h1>

<button onclick={() => showConfetti++}> Confetti Spaghetti! 🎀 </button>

{#if showConfetti > 0}
	{#key showConfetti}
		<Confetti />
	{/key}
{/if}

<games-section>
	<h3>Games</h3>

	<div>
		<a class="button" href="/knoughts-and-crosses"> Knoughts and Crosses </a>
	</div>
</games-section>

<style>
	games-section {
		display: block;

		h3 {
			text-align: center;
		}
		div {
			display: flex;
			justify-content: center;
			align-items: center;
			flex-wrap: wrap;
			max-width: 40rem;
		}
	}
</style>
