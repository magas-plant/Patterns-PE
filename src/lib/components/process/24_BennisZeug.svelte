<script>
	const squareSize = 50;
	let squareDistance = $state(10);

	let combinedOffset = $derived(squareDistance + squareSize);

	// const squareCountX = Math.ceil(500 / combinedOffset) * 2;
	// const squareCountY = Math.ceil(500 / combinedOffset) * 2;
	const squareCountX = 2;
	const squareCountY = 2;

	function calculatePosition(index, count) {
		const basePosition = (index - count / 2) * squareSize;
		const offsetPosition = (index - count / 2 + 0.5) * squareDistance;
		console.log(basePosition + offsetPosition);
		return basePosition + offsetPosition;
	}
</script>

<div class="controls">
	<label>
		Abstand: {squareDistance}px
		<input type="range" bind:value={squareDistance} min="0" max="100" step="1" />
	</label>
</div>

<div class="svg-container">
	<svg viewBox="-500 -500 1000 1000" class="svg-canvas">
		{#each Array(squareCountX) as _, xi}
			{#each Array(squareCountY) as _, yi}
				<rect
					x={calculatePosition(xi, squareCountX)}
					y={calculatePosition(yi, squareCountY)}
					width={squareSize}
					height={squareSize}
					fill={(xi + yi) % 2 == 0 ? 'tomato' : 'dodgerblue'}
					data-x={xi}
					data-y={yi}
				/>
			{/each}
		{/each}

		<ellipse cx="0" cy="0" rx="5" ry="5" fill="white" />
	</svg>
</div>

<style>
	.controls {
		padding: 1rem;
		background-color: #f5f5f5;
		border-radius: 8px;
		margin-bottom: 1rem;
	}

	label {
		display: flex;
		flex-direction: column;
		gap: 0.5rem;
		font-family:
			system-ui,
			-apple-system,
			sans-serif;
		font-size: 14px;
		font-weight: 500;
	}

	input[type='range'] {
		width: 100%;
		cursor: pointer;
	}
</style>
