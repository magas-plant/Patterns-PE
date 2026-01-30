<script>
	import chroma from 'chroma-js';
	import Slider from '$lib/ui/Slider.svelte';
	import Toggle from '$lib/ui/Toggle.svelte';
	import ColorPickerHSV from '$lib/ui/ColorPicker/ColorPickerHSV.svelte';

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

	// Base color selected via color picker
	let baseColor = $state('#802699');

	// Get base color in OKLCH
	let baseOklch = $derived(chroma(baseColor).oklch());
	let baseL = $derived(baseOklch[0] || 0.5);
	let baseC = $derived(baseOklch[1] || 0.2);
	let baseH = $derived(baseOklch[2] || 250);

	// Fixed range values for color generation
	const hueRange = 60; // +/- degrees from base hue
	const lightnessRange = 0.3; // +/- from base lightness
	const chromaRange = 0.15; // +/- from base chroma

	// Toggle for inverted color mode
	let invertedMode = $state(false);

	// Color amount selection (2-7 colors)
	let selectedColorCount = $state(7); // Default: 7 colors
	let colorOptions = [2, 3, 4, 5, 6, 7];

	// Generate all 7 possible colors with two different modes
	let allColors = $derived(
		Array.from({ length: 7 }, (_, i) => {
			const factor = (i / 6) * 2 - 1; // -1 to 1 across 7 colors, with middle (i=3) at 0

			// For colors 2, 3, 4 (indices 1, 2, 3) use inverted/complementary hue in inverted mode
			const useInvertedHue = invertedMode && i >= 1 && i <= 3;
			const hueOffset = useInvertedHue ? 180 : 0; // Complementary color (opposite on color wheel)

			// In inverted mode, use smaller range for inverted colors to keep them similar to each other
			const effectiveHueRange = useInvertedHue ? 30 : hueRange;

			return chroma.oklch(
				Math.max(0.1, Math.min(0.9, baseL + lightnessRange * factor)),
				Math.max(0, Math.min(0.37, baseC + chromaRange * factor)),
				(baseH + hueOffset + effectiveHueRange * factor + 360) % 360
			);
		})
	);

	// Use only the selected number of colors
	let activeColors = $derived(allColors.slice(0, selectedColorCount));

	// Map colors to specific roles
	let color1 = $derived(activeColors[0]); // Background
	let color2 = $derived(activeColors[Math.min(1, activeColors.length - 1)]);
	let color3 = $derived(activeColors[Math.min(2, activeColors.length - 1)]);
	let color4 = $derived(activeColors[Math.min(3, activeColors.length - 1)]);
	let color5 = $derived(activeColors[Math.min(4, activeColors.length - 1)]);
	let color6 = $derived(activeColors[Math.min(5, activeColors.length - 1)]);
	let color7 = $derived(activeColors[Math.min(6, activeColors.length - 1)]);
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
	<Slider min={2} max={20} step="0.1" bind:value={zoomLevel} label="Zoom" />
	<Slider min={0} max={10} step="0.1" bind:value={length} label="Length" />
	<Slider min={0} max={180} bind:value={Rotation} label="Rotation" />

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

	<div class="toggle-section">
		<Toggle bind:value={invertedMode} label="Inverted Color Mode" />
	</div>

	<div class="color-picker-section">
		<label class="section-label">Base Color</label>
		<div class="color-picker-wrapper">
			<ColorPickerHSV bind:color={baseColor} width={280} height={200} />
		</div>
	</div>
</div>

<style>
	.color-picker-section {
		display: flex;
		flex-direction: column;
		margin-top: 20px;
		padding-top: 20px;
		border-top: 1px solid #444;
	}

	.section-label {
		font-size: 0.75rem;
		margin-bottom: 0.5rem;
		color: #ccc;
		user-select: none;
	}

	.color-picker-wrapper {
		width: 100%;
		display: flex;
		justify-content: center;
	}

	.color-amount-section {
		display: flex;
		flex-direction: column;
		width: 100%;
		margin-bottom: 1rem;
	}

	.toggle-section {
		display: flex;
		flex-direction: column;
		width: 100%;
		margin-bottom: 1rem;
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
