<script>
	const squareCount = 9;
	const cos = Math.cos(Math.PI / 4);
	const squareSize = 1000 / (squareCount * (1 + cos));

	const cosSize = cos * squareSize;
	const cos2Size = cosSize * 2;
	const Parallelogramm = `0,0 ${squareSize},0 ${squareSize + cosSize},${cosSize} ${cosSize},${cosSize}`;
	const Rotatepoint = (squareSize + cosSize) / 2;

	function calculatePosition(xi, yi) {
		const x = xi * (squareSize + cosSize);
		const y = yi * (squareSize + cosSize);

		return { x: x, y: y };
	}
</script>

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
						transform="translate({cosSize} 0) rotate(90)"
						points={Parallelogramm}
						fill={(xi + yi) % 2 == 0 ? 'lightblue' : 'coral'}
					/>
					<polygon
						transform="translate(0 {squareSize + cosSize}) scale(1 -1)"
						points={Parallelogramm}
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
	</svg>
</div>
