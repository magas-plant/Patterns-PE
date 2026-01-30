<!-- 
  - HTML und Svelte Code zeigen + Funktionsaufrufe
  - Zeige wie das erste kleine Pattern beginnt (außerhalb der Schleife)

  - 2 Rechtecke in der Schleife und entsprechend der Indizes x, y, width und height anpassen
  - Funktionen müssen sie schreiben 
  - Kleiner Tipp mit data-x um zu sehen welche Indizes die Rects haben
-->

<script>
	const squareCount = 20;
	const squareSize = 1000 / squareCount;

	function isRotated(xi, yi) {
		return (xi + yi) % 2 === 1;
	}

	function calculateSizeCords1(xi, yi) {
		const rotated = isRotated(xi, yi);
		return {
			x: xi * squareSize,
			y: yi * squareSize,
			width: rotated ? squareSize / 2 : squareSize,
			height: rotated ? squareSize : squareSize / 2
		};
	}

	function calculateSizeCords2(xi, yi) {
		const rotated = isRotated(xi, yi);
		return {
			x: rotated ? xi * squareSize + squareSize / 2 : xi * squareSize,
			y: rotated ? yi * squareSize : yi * squareSize + squareSize / 2,
			width: rotated ? squareSize / 2 : squareSize,
			height: rotated ? squareSize : squareSize / 2
		};
	}
</script>

<div class="svg-container">
	<svg viewBox="0 0 1000 1000" class="svg-canvas">
		<!-- Pattern-Schleife mit Funktionsaufrufen -->
		{#each Array(squareCount) as _, yi}
			{#each Array(squareCount) as _, xi}
				{@const coords1 = calculateSizeCords1(xi, yi)}
				{@const coords2 = calculateSizeCords2(xi, yi)}

				<rect
					data-x={xi}
					data-y={yi}
					transform="translate({coords1.x} {coords1.y})"
					width={coords1.width}
					height={coords1.height}
					fill="coral"
				/>

				<rect
					data-x={xi}
					data-y={yi}
					transform="translate({coords2.x} {coords2.y})"
					width={coords2.width}
					height={coords2.height}
					fill="lightblue"
				/>
			{/each}
		{/each}
	</svg>
</div>
