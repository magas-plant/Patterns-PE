<script>
	const squareCount = 7;
	const squareSize = 1000 / squareCount;
	const x = squareSize / 2;
	const deg = 30;
	const rad = (Math.PI / 180) * deg;
	let offset = $state(0);

	const y = Math.tan(rad) * x;
	const z = Math.sin(rad) * x + y;

	const ySize = y + z;

	function calculatePosition(xi, yi) {
		const x = xi * squareSize + xi * offset;
		const y = yi * ySize + yi * offset;

		return { x: x, y: y };
	}
</script>

<div id="control1">
	<input type="range" min="0" max="100" bind:value={offset} />
	<label>{offset}</label>
</div>

<div class="svg-container">
	<svg viewBox="0 0 1000 1000" class="svg-canvas" shape-rendering="crispEdges" background="#4E0250">
		{#each Array(squareCount + 4) as _, yi}
			{#each Array(squareCount + 1) as _, xi}
				<g
					transform="translate({calculatePosition(xi, yi).x +
						(yi % 2 === 0 ? 0 : x)} {calculatePosition(xi, yi).y}) translate({-squareSize / 2} 0)"
				>
					<polygon points="0 0, {x} {y}, {x * 2} 0, {x} {-y} " fill="#8FE388" />

					<polygon
						transform="rotate(120 {x} {y})"
						points="0 0, {x} {y}, {x * 2} 0, {x} {-y} "
						fill="#58BC82"
					/>

					<polygon
						transform="rotate(240 {x} {y})"
						points="0 0, {x} {y}, {x * 2} 0, {x} {-y} "
						fill="#645986"
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
</style>
