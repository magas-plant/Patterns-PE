<script>
	import chroma from 'chroma-js';
	import Slider from '$lib/ui/Slider.svelte';
	import RangeSlider from '$lib/ui/RangeSlider.svelte';

	let squareCount = $state(8);
	let length = $state(1);

	// Zoom control: defines how much of the pattern is visible (2-20)
	let zoomLevel = $state(8); // Default: show 8x8 patterns

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

	let gridSpacing = $derived(squareSize + cosSize + offset);
	let totalGridSize = $derived((squareCount + 25) * gridSpacing);
	let centerOffset = $derived(totalGridSize / 2);

	// Dynamic viewBox calculation for smooth zoom
	// When zoomLevel = squareCount, show the whole pattern (viewBox = "0 0 1000 1000")
	// When zoomLevel < squareCount, zoom in (smaller viewBox)
	let viewBoxSize = $derived((1000 * zoomLevel) / squareCount);
	let viewBoxOffset = $derived((1000 - viewBoxSize) / 2);
	let dynamicViewBox = $derived(`${viewBoxOffset} ${viewBoxOffset} ${viewBoxSize} ${viewBoxSize}`);

	function calculatePosition(xi, yi) {
		const x = xi * gridSpacing - centerOffset + 500;
		const y = yi * gridSpacing - centerOffset + 500;

		return { x: x, y: y };
	}

	let huemin = $state(222);
	let huemax = $state(360);
	let lightnessmin = $state(0.13);
	let lightnessmax = $state(0.63);
	let chromamin = $state(0.17);
	let chromamax = $state(0.31);

	// Color amount selection (2-7 colors)
	let selectedColorCount = $state(7); // Default: 7 colors
	let colorOptions = [2, 3, 4, 5, 6, 7];

	// Preset functions
	function applyPreset1() {
		squareCount = 8;
		zoomLevel = 3;
		length = 0.6;
		Rotation = 0;
		huemin = 0;
		huemax = 88;
		lightnessmin = 0.28;
		lightnessmax = 0.78;
		chromamin = 0.22;
		chromamax = 0.29;
		selectedColorCount = 7;
	}

	function applyPreset2() {
		squareCount = 8;
		zoomLevel = 5;
		length = 0.7;
		Rotation = 45;
		huemin = 149;
		huemax = 204;
		lightnessmin = 0.39;
		lightnessmax = 0.76;
		chromamin = 0.19;
		chromamax = 0.5;
		selectedColorCount = 6;
	}

	function applyPreset3() {
		squareCount = 10;
		zoomLevel = 7;
		length = 1.7;
		Rotation = 70;
		huemin = 201;
		huemax = 241;
		lightnessmin = 0.1;
		lightnessmax = 0.8;
		chromamin = 0.1;
		chromamax = 0.1;
		selectedColorCount = 6;
	}

	// Generate all 7 possible colors
	let allColors = $derived(
		Array.from({ length: 7 }, (_, i) => {
			const factor = i / 6; // 0 to 1 across 7 colors
			return chroma.oklch(
				lightnessmin + (lightnessmax - lightnessmin) * factor,
				chromamin + (chromamax - chromamin) * factor,
				huemin + (huemax - huemin) * factor
			);
		})
	);

	// Use only the selected number of colors
	let activeColors = $derived(allColors.slice(0, selectedColorCount));

	// Map colors to specific roles
	let color1 = '#000000'; // Background - always black
	let color2 = $derived(activeColors[Math.min(0, activeColors.length - 1)]);
	let color3 = $derived(activeColors[Math.min(1, activeColors.length - 1)]);
	let color4 = $derived(activeColors[Math.min(2, activeColors.length - 1)]);
	let color5 = $derived(activeColors[Math.min(3, activeColors.length - 1)]);
	let color6 = $derived(activeColors[Math.min(4, activeColors.length - 1)]);
	let color7 = $derived(activeColors[Math.min(5, activeColors.length - 1)]);
</script>

<div class="svg-container">
	<svg
		viewBox={dynamicViewBox}
		class="svg-canvas"
		shape-rendering="crispEdges"
		style={'background-color: ' + color1}
	>
		<g transform="rotate({Rotation} 500 500)">
			{#each Array(squareCount + 25) as _, yi}
				{#each Array(squareCount + 25) as _, xi}
					{@const isEven = (xi + yi) % 2 === 0}
					{@const poly1Fill =
						selectedColorCount === 4 ? (isEven ? color4 : color2) : isEven ? color2 : color5}
					{@const poly2Fill =
						selectedColorCount === 4 ? (isEven ? color3 : color4) : isEven ? color3 : color6}
					{@const rectFill =
						selectedColorCount === 4 ? (isEven ? color2 : color3) : isEven ? color4 : color7}
					<g
						transform="translate({calculatePosition(xi, yi).x} {calculatePosition(xi, yi)
							.y}) translate({-cosSize / 2} {-cosSize / 2}) scale({isEven ? 1 : -1}) rotate({isEven
							? 0
							: 270} {Rotatepoint} {Rotatepoint})"
					>
						<polygon
							transform="translate({cosSize} 0) rotate(90)"
							points={Parallelogramm}
							fill={poly1Fill}
							stroke={poly1Fill}
							stroke-width="1.5"
						/>
						<polygon
							points={Parallelogramm2}
							fill={poly2Fill}
							stroke={poly2Fill}
							stroke-width="1.5"
						/>
						<rect
							transform="translate({cosSize} 0)"
							width={squareSize}
							height={squareSize}
							fill={rectFill}
							stroke={rectFill}
							stroke-width="1.5"
						/>
					</g>
				{/each}
			{/each}
		</g>
	</svg>
</div>

<div class="sidebar-right">
	<div class="preset-section">
		<div class="label">Presets</div>
		<div class="preset-buttons">
			<button class="preset-btn" onclick={applyPreset1}>1</button>
			<button class="preset-btn" onclick={applyPreset2}>2</button>
			<button class="preset-btn" onclick={applyPreset3}>3</button>
		</div>
	</div>

	<Slider min={2} max={20} step="0.1" bind:value={zoomLevel} label="Zoom" />
	<Slider min={0} max={10} step="0.1" bind:value={length} label="Length" />
	<Slider min={0} max={180} bind:value={Rotation} label="Rotation" />

	<RangeSlider min={0} max={360} bind:value1={huemin} bind:value2={huemax} label="Hue Range" />

	<RangeSlider
		min={0.1}
		max={0.8}
		step={0.01}
		bind:value1={lightnessmin}
		bind:value2={lightnessmax}
		label="Lightness Range"
	/>

	<RangeSlider
		min={0.1}
		max={0.7}
		step={0.01}
		bind:value1={chromamin}
		bind:value2={chromamax}
		label="Chroma Range"
	/>

	<div class="color-amount-section">
		<label class="section-label">Number of Colors</label>
		<div class="radio-group">
			{#each colorOptions as count}
				<label class="radio-label">
					<input
						type="radio"
						name="colorCount"
						value={count}
						checked={selectedColorCount === count}
						onchange={() => (selectedColorCount = count)}
					/>
					<span class="radio-text">{count}</span>
				</label>
			{/each}
		</div>
	</div>
</div>

<style>
	.preset-section {
		display: flex;
		flex-direction: column;
		margin-bottom: 20px;
		padding-bottom: 20px;
		border-bottom: 1px solid #444;
	}

	.preset-buttons {
		display: flex;
		gap: 10px;
		flex-wrap: wrap;
	}

	.preset-btn {
		padding: 10px 20px;
		background-color: #666;
		color: white;
		border: none;
		border-radius: 5px;
		cursor: pointer;
		font-size: 0.875rem;
		font-weight: 500;
		transition: background-color 0.2s;
		flex: 1;
		min-width: 90px;
	}

	.preset-btn:hover {
		background-color: #666;
	}

	.preset-btn:active {
		background-color: #777;
		transform: translateY(1px);
	}

	.color-amount-section {
		display: flex;
		flex-direction: column;
		width: 100%;
		margin-bottom: 1rem;
	}

	.section-label {
		font-size: 0.75rem;
		margin-bottom: 0.3rem;
		color: #ccc;
		user-select: none;
	}

	.radio-group {
		display: flex;
		gap: 4px;
		background: #444;
		border-radius: 4px;
		padding: 2px;
	}

	.radio-label {
		flex: 1;
		position: relative;
		cursor: pointer;
	}

	.radio-label input[type='radio'] {
		position: absolute;
		opacity: 0;
		width: 0;
		height: 0;
	}

	.radio-text {
		display: block;
		text-align: center;
		padding: 4px 8px;
		font-size: 0.75rem;
		color: #ccc;
		background: transparent;
		border-radius: 3px;
		transition: all 0.15s ease;
		user-select: none;
	}

	.radio-label input[type='radio']:checked + .radio-text {
		background: #666;
		color: #fff;
		border: 1px solid #777;
	}

	.radio-label:hover .radio-text {
		color: #fff;
	}
</style>
