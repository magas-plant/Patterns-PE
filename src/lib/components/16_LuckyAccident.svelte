<script>
	let squareCount = $state(20);
	let squareSize = $derived(1000 / squareCount);
	const cos = Math.cos(Math.PI / 4);
	const sin = Math.sin(Math.PI / 4);
	let cosSize = $derived(cos * squareSize);
	let sinSize = $derived(sin * squareSize);
	let cos2Size = $derived(cosSize * 2);
	let Parallelogramm = $derived(
		`0,0 ${squareSize},0 ${squareSize + cosSize},${sinSize} ${cosSize},${sinSize}`
	);
	const Parallelogramm2 = $derived(
		`0, ${squareSize} ${cosSize}, ${sinSize + squareSize} ${cosSize}, ${2 * squareSize + sinSize}, 0, ${squareSize * 2}`
	);

	function calculatePosition(xi, yi) {
		const x = xi * (squareSize + cosSize);
		const y = yi * (2 * squareSize + sinSize);

		return { x: x, y: y };
	}
</script>

<div id="control1">
	<input type="range" min="1" max="50" bind:value={squareCount} />
</div>

<div class="svg-container">
	<svg viewBox="0 0 1000 1000" class="svg-canvas" shape-rendering="crispEdges">
		{#each Array(squareCount) as _, yi}
			{#each Array(squareCount) as _, xi}
				<g
					transform="translate({calculatePosition(xi, yi).x +
						((xi + yi) % 2 === 0 ? 0 : squareSize + cosSize)} {calculatePosition(xi, yi)
						.y}) scale({(xi + yi) % 2 === 0 ? 1 : -1}, 1)"
				>
					<polygon points={Parallelogramm} fill="lightblue" />

					<rect
						transform="translate({cosSize} {sinSize})"
						width={squareSize}
						height={squareSize}
						fill="tomato"
					/>

					<rect
						transform="translate({cosSize} {sinSize + squareSize})"
						width={squareSize}
						height={squareSize}
						fill="coral"
					/>
					<polygon points={Parallelogramm2} fill="blueviolet" />
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
