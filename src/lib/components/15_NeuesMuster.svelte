<script>
	const squareCount = 20;
	const squareSize = 1000 / squareCount;
	const cos = Math.cos(Math.PI / 4);
	const sin = Math.sin(Math.PI / 4);
	const cosSize = cos * squareSize;
	const sinSize = sin * squareSize;
	const cos2Size = cosSize * 2;
	const Parallelogramm = `0,0 ${squareSize},0 ${squareSize + cosSize},${sinSize} ${cosSize},${sinSize}`;
	const Parallelogramm2 = `0, ${squareSize} ${cosSize}, ${sinSize + squareSize} ${cosSize}, ${2 * squareSize + sinSize}, 0, ${squareSize * 2}`;

	function calculatePosition(xi, yi) {
		const x = xi * (squareSize + cosSize);
		const y = yi * (2 * squareSize + sinSize);

		return { x: x, y: y };
	}
</script>

<div class="svg-container">
	<svg viewBox="0 0 1000 1000" class="svg-canvas" shape-rendering="crispEdges">
		{#each Array(squareCount) as _, yi}
			{#each Array(squareCount + 1) as _, xi}
				<g
					transform="translate({xi * (squareSize + cosSize) +
						((xi + yi) % 2 === 0 ? 0 : squareSize + cosSize) -
						(squareSize + cosSize)} {yi * (2 * squareSize + sinSize)}) scale(1, {(xi + yi) % 2 === 0
						? 1
						: -1})"
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
