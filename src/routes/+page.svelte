<script>
	import Puzzle from '$lib/Puzzle.svelte'	
	let num = Math.floor(Math.random()*300)
	let win = false

	function reset() {
        win = false
		num = Math.floor(Math.random()*1000)
	}
</script>

<section>
	<h1>#{num}</h1>
{#key num}
	<Puzzle {num} bind:win/>
		<br>
		<button 
			style:pointer-events={win? "auto" : "none"}
			style:opacity={win? 1 : 0}
			disabled={!win}
			on:click={reset}>Reset Puzzle</button>
{/key}
</section>

<style>
	section {
		width:100%;
		display:flex;
		justify-content:center;
		align-items:center;
		height:90vh;
		flex-direction:column;
		text-align:center;
		pointer-events:none;
	}

    :global(body) {
        background-color: #1C1D21;
        font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        color:#C4F582;
    }

    h1 {
        font-family: "BBH Bogle", sans-serif;
        font-weight: 400;
        font-style: normal;
        font-size:5rem;
    }
    
    button {
        font-family: "BBH Bogle", sans-serif;
        font-weight: 400;
        font-style: normal;
        background-color: #C4F582;
        color: #1C1D21;
        font-size: 1rem;
        border:none;
    }
	
</style>

