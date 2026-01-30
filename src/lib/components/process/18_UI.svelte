<script>
	import chroma from 'chroma-js';
	import Slider from '$lib/ui/Slider.svelte';

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

	let hue = $state(120);
	let lightness = $state(0.5);
	let chromavalue = $state(0.2);
	let color1 = $derived(chroma.oklch(lightness, chromavalue, hue + 60));
	let color2 = $derived(chroma.oklch(lightness, chromavalue, hue + 120));
	let color3 = $derived(chroma.oklch(lightness, chromavalue, hue));
	let color4 = $derived(chroma.oklch(lightness - 0.4, chromavalue, hue + 180));
</script>

<div class="sidebar">
	<h3 style="margin: 0 0 10px 0; font-size: 1rem; font-weight: 500;">Pattern Controls</h3>

	<Slider min={0} max={1} step="0.01" bind:value={lightness} label="Lightness" />
	<Slider min={0} max={1} step="0.01" bind:value={chromavalue} label="Chroma" />
	<Slider min={0} max={360} bind:value={hue} label="Hue" />
</div>

<div class="svg-container">
	<svg
		viewBox="0 0 1000 1000"
		class="svg-canvas"
		shape-rendering="crispEdges"
		style={'background-color: ' + color4}
	>
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
						fill={(xi + yi) % 2 == 0 ? color1 : color2}
					/>
					<polygon
						transform="translate(0 {squareSize + cosSize}) scale(1 -1)"
						points={Parallelogramm}
						fill={(xi + yi) % 2 == 0 ? color3 : color1}
					/>
					<rect
						transform="translate({cosSize} 0)"
						width={squareSize}
						height={squareSize}
						fill={(xi + yi) % 2 == 0 ? color2 : color3}
					/>
				</g>
			{/each}
		{/each}
	</svg>
</div>

<style>
	div.sidebar {
		display: flex;
		flex-direction: column;
		list-style: none;
		padding: 20px;
		margin: 0 0 1rem 0;
		gap: 20px;
		justify-content: flex-start;
		overflow-y: auto;
		min-width: 350px;
	}
</style>
