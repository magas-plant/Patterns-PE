<script>
	const squareCount = 20;
	const squareSize = 1000 / squareCount;
	const cos = Math.cos(Math.PI / 4);
	const cosSize = cos * squareSize;
	const cos2Size = cosSize * 2;
	const Parallelogramm = `0,0 ${squareSize},0 ${squareSize + cosSize},${cosSize} ${cosSize},${cosSize}`;
	const Parallelogramm2 = `${cosSize}, ${squareSize} ${cosSize + squareSize}, ${squareSize} ${squareSize}, ${squareSize + cosSize}, 0, ${squareSize + cosSize}`;

	function calculatePosition(xi, yi) {
		const x = xi * 2 * squareSize;
		const y = yi * 2 * squareSize;

		return { x: x, y: y };
	}
</script>

<div class="svg-container">
	<svg viewBox="0 0 1000 1000" class="svg-canvas" shape-rendering="crispEdges">
		<g>
			<polygon
				transform="translate({cosSize} 0) rotate(90)"
				points={Parallelogramm}
				fill="lightblue"
			/>
			<polygon points={Parallelogramm2} fill="blueviolet" />
			<rect
				transform="translate({cosSize} 0)"
				width={squareSize}
				height={squareSize}
				fill="coral"
			/>
		</g>
	</svg>
</div>
