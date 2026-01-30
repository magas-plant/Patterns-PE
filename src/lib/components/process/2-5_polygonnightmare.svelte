<script>
	let squareCount = $state(20);
	let squareSize = $derived(1000 / squareCount);

	let offset = $state(0);

	function calculatePosition(xi, yi) {
		const x = xi * (squareSize + offset);
		const y = yi * (squareSize + offset);

		return { x: x, y: y };
	}
</script>

<div id="control2">
	<input type="range" min="1" max="100" bind:value={squareCount} />
	<label>{squareCount}</label>
</div>

<div class="svg-container">
	<svg viewBox="0 0 1000 1000" class="svg-canvas">
		{#each Array(squareCount) as _, xi}
			{#each Array(squareCount + 1) as _, yi}
				<g transform="translate({calculatePosition(xi, yi).x} {calculatePosition(xi, yi).y})">
					<polygon points="0,0 {squareSize},0 0,{squareSize}" fill="coral" />
					<polygon
						points="{squareSize},0 {squareSize},{squareSize} 0,{squareSize}"
						fill="lightblue"
					/>
				</g>
			{/each}
		{/each}
	</svg>
</div>

<style>
	#control1 {
		display: flex;
		width: 150px;
		justify-content: left;
		align-items: center;
	}
	input {
		width: 50%;
	}

	#control2 {
		display: flex;
		width: 150px;
		justify-content: left;
		align-items: center;
	}
	input {
		width: 50%;
	}
</style>
