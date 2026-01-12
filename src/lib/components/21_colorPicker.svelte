<script>
	import chroma from 'chroma-js';
	import Slider from '$lib/ui/Slider.svelte';
	import ColorPickerHSV from '$lib/ui/ColorPicker/ColorPickerHSV.svelte';
	import EditableColorPalette from '$lib/ui/EditableColorPalette.svelte';

	let squareCount = $state(8);
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

	let colors = $state(['#552255', '#00ffaa']);
	let selectedColorIndex = $state(0);

	let color1 = $derived(colors[0] || '#552255');
	let color2 = $derived(colors[1] || '#00ffaa');

	function patternClick(event) {
		const colorIndex = event.target.getAttribute('data-color-index');
		selectedColorIndex = parseInt(colorIndex, 10);
	}

	function patternKeyDown(event) {
		if (event.key === 'Enter' || event.key === ' ') {
			event.preventDefault();
			const colorIndex = event.target.getAttribute('data-color-index');
			selectedColorIndex = parseInt(colorIndex, 10);
		}
	}
</script>

<div class="color-section">
	<h3 style="margin: 0 0 10px 0; font-size: 1rem; font-weight: 500;">Polygon Colors</h3>
	<EditableColorPalette
		bind:colors
		bind:selectedColorIndex
		width={310}
		height={310}
		swatchSize={30}
	/>
</div>

<div class="sidebar">
	<h3 style="margin: 0 0 10px 0; font-size: 1rem; font-weight: 500;">Pattern Controls</h3>

	<Slider min={0} max={100} bind:value={squareCount} label="Size" />
	<Slider min={0} max={10} step="0.1" bind:value={length} label="Length" />
	<Slider min={0} max={360} bind:value={Rotation} label="Rotation" />
</div>

<div class="svg-container">
	<svg
		viewBox="0 0 1000 1000"
		class="svg-canvas"
		shape-rendering="crispEdges"
		style={'background-color: ' + color1}
	>
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
							fill={(xi + yi) % 2 == 0 ? color1 : color2}
							data-color-index="1"
						/>
						<polygon
							points={Parallelogramm2}
							fill={(xi + yi) % 2 == 0 ? color2 : color1}
							data-color-index="0"
						/>
						<rect
							transform="translate({cosSize} 0)"
							width={squareSize}
							height={squareSize}
							fill={(xi + yi) % 2 == 0 ? color2 : color1}
							data-color-index="1"
						/>
					</g>
				{/each}
			{/each}
		</g>
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
