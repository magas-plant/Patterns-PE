<script>
	let squareCount = $state(9);
	let offset = $state(0);
	let diamondLength = $state(1.0); // Streckfaktor für die Raute (0.5 - 2.0)

	let cos = Math.cos(Math.PI / 4);
	let squareSize = $derived(1000 / (squareCount * (1 + cos)));

	let cosSize = $derived(cos * squareSize);
	let cosSizeStretched = $derived(cosSize * diamondLength); // Gestreckte Raute

	/**
	 * Berechnet die Punkte für das erste Parallelogramm (Raute)
	 * Wird in horizontaler Richtung durch diamondLength gestreckt
	 */
	function getRhombusPoints1(baseSize, stretchedCosSize) {
		return `0,0 ${baseSize},0 ${baseSize + stretchedCosSize},${cosSize} ${stretchedCosSize},${cosSize}`;
	}

	/**
	 * Berechnet die Punkte für das zweite Parallelogramm (Raute)
	 * Wird in horizontaler Richtung durch diamondLength gestreckt
	 */
	function getRhombusPoints2(baseSize, stretchedCosSize) {
		return `${stretchedCosSize}, ${baseSize} ${stretchedCosSize + baseSize}, ${baseSize} ${baseSize}, ${baseSize + cosSize}, 0, ${baseSize + cosSize}`;
	}

	let Parallelogramm = $derived(getRhombusPoints1(squareSize, cosSizeStretched));
	let Parallelogramm2 = $derived(getRhombusPoints2(squareSize, cosSizeStretched));

	// Rotationspunkt basiert auf der gestreckten Geometrie
	let Rotatepoint = $derived((squareSize + cosSizeStretched) / 2);

	/**
	 * Berechnet die Position eines Tiles im Grid
	 * Berücksichtigt die gestreckte Breite und den Gap
	 */
	function calculatePosition(xi, yi) {
		const tileWidth = squareSize + cosSizeStretched;
		const x = xi * (tileWidth + offset);
		const y = yi * (tileWidth + offset);

		return { x: x, y: y };
	}
</script>

<div class="controls">
	<div class="control-group">
		<label for="tilesize-slider">Tile Size: {squareCount}</label>
		<input id="tilesize-slider" type="range" min="1" max="50" bind:value={squareCount} />
	</div>
	<div class="control-group">
		<label for="gap-slider">Gap: {offset}</label>
		<input id="gap-slider" type="range" min="0" max="100" bind:value={offset} />
	</div>
	<div class="control-group">
		<label for="diamond-slider">Diamond Length: {diamondLength.toFixed(2)}</label>
		<input
			id="diamond-slider"
			type="range"
			min="0.5"
			max="2.0"
			step="0.01"
			bind:value={diamondLength}
		/>
	</div>
</div>

<div class="svg-container">
	<svg viewBox="0 0 1000 1000" class="svg-canvas" shape-rendering="crispEdges">
		{#each Array(squareCount + 1) as _, yi}
			{#each Array(squareCount + 1) as _, xi}
				<g
					transform="translate({calculatePosition(xi, yi).x} {calculatePosition(xi, yi)
						.y}) scale({(xi + yi) % 2 === 0 ? 1 : -1}) rotate({(xi + yi) % 2 === 0
						? 0
						: 270} {Rotatepoint} {Rotatepoint})"
				>
					<polygon
						transform="translate({cosSizeStretched} 0) rotate(90)"
						points={Parallelogramm}
						fill={(xi + yi) % 2 == 0 ? 'lightblue' : 'coral'}
					/>
					<polygon
						points={Parallelogramm2}
						fill={(xi + yi) % 2 == 0 ? 'blueviolet' : 'lightblue'}
					/>
					<rect
						transform="translate({cosSizeStretched} 0)"
						width={squareSize}
						height={squareSize}
						fill={(xi + yi) % 2 == 0 ? 'coral' : 'blueviolet'}
					/>
				</g>
			{/each}
		{/each}
	</svg>
</div>

<style>
	.controls {
		display: flex;
		flex-direction: column;
		gap: 15px;
		padding: 20px;
		background: #f5f5f5;
		border-radius: 8px;
		margin-bottom: 20px;
	}

	.control-group {
		display: flex;
		flex-direction: column;
		gap: 5px;
	}

	.control-group label {
		font-size: 14px;
		font-weight: 500;
		color: #333;
	}

	input[type='range'] {
		width: 100%;
		max-width: 300px;
	}

	.svg-container {
		width: 100%;
		max-width: 800px;
		margin: 0 auto;
	}

	.svg-canvas {
		width: 100%;
		height: auto;
		border: 1px solid #ddd;
	}
</style>
