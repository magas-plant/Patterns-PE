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

	let colors = $state(['#552255', '#00ffaa', '#ff5522', '#55aaff']);
	let selectedColorIndex = $state(0);

	let color1 = $derived(colors[0] || '#552255');
	let color2 = $derived(colors[1] || '#00ffaa');
	let color3 = $derived(colors[2] || '#ff5522');
	let color4 = $derived(colors[3] || '#55aaff');

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

<div class="svg-container">
	<svg
		viewBox="0 0 1000 1000"
		class="svg-canvas"
		shape-rendering="crispEdges"
		style={'background-color: ' + color1}
		on:click={patternClick}
		on:keydown={patternKeyDown}
		data-color-index="0"
		role="button"
		tabindex="0"
	>
		<g transform="rotate({Rotation} 500 500)">
			{#each Array(squareCount + 25) as _, yi}
				{#each Array(squareCount + 25) as _, xi}
					<g
						transform="translate({calculatePosition(xi, yi).x} {calculatePosition(xi, yi)
							.y}) translate({-cosSize / 2} {-cosSize / 2}) scale({(xi + yi) % 2 === 0
							? 1
							: -1}) rotate({(xi + yi) % 2 === 0 ? 0 : 270} {Rotatepoint} {Rotatepoint})"
					>
						<polygon
							transform="translate({cosSize} 0) rotate(90)"
							points={Parallelogramm}
							fill={(xi + yi) % 2 == 0 ? color4 : color2}
							data-color-index={(xi + yi) % 2 == 0 ? '3' : '1'}
						/>
						<polygon
							points={Parallelogramm2}
							fill={(xi + yi) % 2 == 0 ? color3 : color4}
							data-color-index={(xi + yi) % 2 == 0 ? '2' : '3'}
						/>
						<rect
							transform="translate({cosSize} 0)"
							width={squareSize}
							height={squareSize}
							fill={(xi + yi) % 2 == 0 ? color2 : color3}
							data-color-index={(xi + yi) % 2 == 0 ? '1' : '2'}
						/>
					</g>
				{/each}
			{/each}
		</g>
	</svg>
</div>

<div class="sidebar-right">
	<Slider min={0} max={100} bind:value={squareCount} label="Size" />
	<Slider min={0} max={10} step="0.1" bind:value={length} label="Length" />
	<Slider min={0} max={360} bind:value={Rotation} label="Rotation" />

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
</div>
