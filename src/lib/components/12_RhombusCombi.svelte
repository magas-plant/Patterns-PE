<script>
	let squareCount = $state(9);
	let length = $state(1);

	let cos = Math.cos(Math.PI / 4);
	let squareSize = $derived(1000 / (squareCount * (1 + cos)));

	let cosSize = $derived(cos * squareSize * length);

	let Parallelogramm = $derived(
		`0,0 ${squareSize},0 ${squareSize + cosSize},${cosSize} ${cosSize},${cosSize}`
	);
	let Parallelogramm2 = $derived(
		`${cosSize}, ${squareSize} ${cosSize + squareSize}, ${squareSize} ${squareSize}, ${squareSize + cosSize}, 0, ${squareSize + cosSize}`
	);

	let Rotatepoint = $derived((squareSize + cosSize) / 2);
	let offset = $state(0);

	let Rotation = $state(0);

	function calculatePosition(xi, yi) {
		const x = xi * (squareSize + cosSize + offset) - 500;
		const y = yi * (squareSize + cosSize + offset) - 500;

		return { x: x, y: y };
	}
</script>

<div id="control1">
	<input type="range" min="2" max="30" bind:value={squareCount} />
</div>
<div id="control2">
	<input type="range" min="1" max="5" step="0.1" bind:value={length} />
</div>
<div id="control2">
	<input type="range" min="0" max="360" bind:value={Rotation} />
</div>

<div class="svg-container">
	<svg viewBox="0 0 1000 1000" class="svg-canvas" shape-rendering="crispEdges">
		<g transform="rotate({Rotation} 500 500)">
			{#each Array(squareCount + 25) as _, yi}
				{#each Array(squareCount + 25) as _, xi}
					<g
						transform="translate({calculatePosition(xi, yi).x} {calculatePosition(xi, yi)
							.y}) scale({(xi + yi) % 2 === 0 ? 1 : -1}) rotate({(xi + yi) % 2 === 0
							? 0
							: 270} {Rotatepoint} {Rotatepoint})"
					>
						<polygon
							transform="translate({cosSize} 0) rotate(90)"
							points={Parallelogramm}
							fill={(xi + yi) % 2 == 0 ? 'lightblue' : 'coral'}
						/>
						<polygon
							points={Parallelogramm2}
							fill={(xi + yi) % 2 == 0 ? 'blueviolet' : 'lightblue'}
						/>
						<rect
							transform="translate({cosSize} 0)"
							width={squareSize}
							height={squareSize}
							fill={(xi + yi) % 2 == 0 ? 'coral' : 'blueviolet'}
						/>
					</g>
				{/each}
			{/each}
		</g>
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
