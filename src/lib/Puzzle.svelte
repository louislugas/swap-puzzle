<script>
    import { onMount } from 'svelte'

	export let num = 0
	export let win = false
	
	let url = "https://picsum.photos/id/"+ num +"/300/300"
	let loaded = false
	let squares = []
	let globalClick = false
	let clickCounter = 0
	let source = 0
	let match = []
	let showWin = false

	function preloadImage(url) {
        console.log(url)
        const img = new Image();
        
        img.onload = function() {
            console.log(`Image from ${url} has loaded successfully.`);
                loaded = true
                gameStart()
        };
	
	  img.onerror = function() {
	    console.error(`Error loading image from ${url}.`);
	  };
	  img.src = url;
	}

    onMount(() => {
        preloadImage(url)
    });

	function gameStart() {
		squares = []
		source = 0
		match = []
		win = false
		
		for(let i = 0; i < 25; i++) {
			squares.push({
				id:i,
				idPos:i,
				active:false,
				color:`rgb(${Math.floor(Math.random()*255)},${Math.floor(Math.random()*255)},${Math.floor(Math.random()*255)})`,
			})
		}

		shuffle(squares)

		squares = squares
	}
	
	function shuffle(array) {
	  let currentIndex = array.length, temporaryValue, randomIndex;
	
	  while (currentIndex !== 0) {
	    randomIndex = Math.floor(Math.random() * currentIndex);
	    currentIndex -= 1;
	
	    temporaryValue = array[currentIndex];
	    array[currentIndex] = array[randomIndex];
	    array[randomIndex] = temporaryValue;
	  }
	
	  return array;
	}

	function handleClick(e) {
		let i = e.target.dataset.index
		let id = squares.findIndex(d => d.id == i)
		
		squares[id].active = true
		if (!globalClick) {
			globalClick = true
			clickCounter++
			source = id
		} else if (globalClick) {
			if (id != source) {
				if (clickCounter == 1) {
					let s = squares[source].idPos
					let t = squares[id].idPos
					
					squares[source].idPos = parseInt(t)
					squares[id].idPos = parseInt(s)
					
					clickCounter = 0
					globalClick = false
					squares.forEach(d => d.active = false)
					source= 0
				}
			}
		}
		squares = squares
	}

	function checkSquares() {
		
		let checkMatch = match.map(d => {
		let iC = d.dataset.indexChecker
		let iP = d.dataset.indexPos
			return iC == iP
		})
	
		let trueMatch = checkMatch.filter(d => d == true)
	
		if (trueMatch.length == checkMatch.length) {
			win = true
		}
	}


		$: if (squares && squares.length > 0) {
		setTimeout(() => {
			checkSquares()
		},100)
	}


		$: if (win && squares.length > 0) {
		setTimeout(() => {
			showWin = true
		},500)
	}
	
</script>
{#if !loaded}
	<div class="loading-container">
		<div class="loading">
			<div class="center"></div>
		</div>
	</div>
	<p>Loading puzzle...</p>
{:else}
	<div class="container">
		<svg style:width="300px" style:height="300px" style:pointer-events="auto">
			{#each squares as square,i}
				<defs>
					<clipPath id="mask">
						<rect 
							x={square.idPos % 5*60} y={Math.floor(square.idPos / 5)*60}
							width="60" height="60"></rect>
					</clipPath>
				</defs>
				<rect 
					bind:this={match[i]}
					on:click={handleClick}
					data-index={square.id}
					data-index-pos={square.idPos}
					data-index-checker={i}
					x={square.idPos % 5*60} y={Math.floor(square.idPos / 5) * 60}
					width="60" height="60" 
					fill="transparent"/>
				
				<g style:transform="translate({square.idPos % 5*60}px, {Math.floor(square.idPos / 5)*60}px)">
					<foreignObject 
						x={0} y={0}
						width="60" height="60">
						<div
							style:background-image="url({url})"
							style:background-position="left {-i % 5 * 60}px top {-Math.floor(i/5)*60}px"
							></div>
					</foreignObject>
				</g>
				 
				<rect 
					class="border"
					data-index={square.id}
					x={square.idPos % 5*60 + 2} y={Math.floor(square.idPos / 5)*60 + 2}
					width="56" height="56" 
					fill=transparent
					stroke="#C4F582" stroke-width="4"
					opacity={square.active ? 1 : 0}/>
			{/each}
		</svg>
		<svg 
			class="win"
			style:width="300px" style:height="300px"
			style:display={showWin ? "block" : "none"}
			>
			<rect
				stroke="#C4F582"
				stroke-width="10"
				fill="transparent"
				/>
			</svg>
	</div>
{/if}


<!-- <img src={url} alt="reference"> -->

<style>
	@property --angle {
	  syntax: '<angle>'; /* Specifies the data type for animation */
	  inherits: false;
	  initial-value: 15deg;
	}
	@property --xy {
	  syntax: '<percentage>'; /* Specifies the data type for animation */
	  inherits: false;
	  initial-value: 0%;
	}
	@property --wh {
	  syntax: '<percentage>'; /* Specifies the data type for animation */
	  inherits: false;
	  initial-value: 100%;
	}
	@property --s {
	  syntax: '<length>'; /* Specifies the data type for animation */
	  inherits: false;
	  initial-value: 10px;
	}
	rect {
		transition:x 500ms ease-in-out, y 500ms ease-in-out;
	}
	
	.border {
		pointer-events: none;
	}
	.container {
		width:300px;
		height:300px;
		position:relative;
	}
	foreignObject {
		pointer-events:none;
	}
	g {
		transition: 500ms ease-in-out;
	}
	div {
		pointer-events:none; 
		width:100px;
		height:100px;
	}
	.loading-container {
		width:300px;
		height:calc(300px - 1rem);
		display:flex;
		justify-content:center;
		align-items:center;
	}
	.loading {
		width:50px;
		height:50px;
		border-radius:50%;
		background-image:conic-gradient(
		  white var(--angle), 
		  transparent var(--angle)
		);
		animation: rotate-gradient 3000ms linear infinite;
		display:flex;
		justify-content:center;
		align-items:center;
	}
	.center {
		width:30px; 
		height:30px;
		border-radius:50%;
		background-color:#1C1D21;
	}
	p {
		height:1rem;
		margin:0;
	}
	@keyframes rotate-gradient {
	  to {
	    --angle: 360deg; /* Animate the angle from 0deg (initial-value) to 360deg */
	  }
	}
	.win {
		position:absolute;
		top:0;
		left:0;
		overflow:visible;
	}
	.win > rect {
		x: var(--xy);
		y: var(--xy);
		width: var(--wh);
		height: var(--wh);
		stroke-width:var(--s);
		animation: win 1000ms ease-out infinite;
	}
	@keyframes win {
		to {
			--xy: -10%;
			--wh:120%;
			--s:0px;
		}
	}
</style>