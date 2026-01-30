<script>
	import chroma from 'chroma-js';
	import Slider from '$lib/ui/Slider.svelte';
	import RangeSlider from '$lib/ui/RangeSlider.svelte';

	let squareCount = $state(16);
	let length = $state(1);
	let Rotation = $state(0);

	let squareSize = $derived(1000 / squareCount);
	let cos = Math.cos(Math.PI / 4);
	let sin = Math.sin(Math.PI / 4);
	let cosSize = $derived(cos * squareSize * length);
	let sinSize = $derived(sin * squareSize * length);

	let Parallelogramm = $derived(
		`0,0 ${squareSize},0 ${squareSize + cosSize},${sinSize} ${cosSize},${sinSize}`
	);
	let Parallelogramm2 = $derived(
		`0, ${squareSize} ${cosSize}, ${sinSize + squareSize} ${cosSize}, ${2 * squareSize + sinSize}, 0, ${squareSize * 2}`
	);

	// Grid calculation for rotation without gaps
	let gridSpacingX = $derived(squareSize + cosSize);
	let gridSpacingY = $derived(2 * squareSize + sinSize);
	let totalGridSizeX = $derived((squareCount + 30) * gridSpacingX);
	let totalGridSizeY = $derived((squareCount + 30) * gridSpacingY);
	let centerOffsetX = $derived(totalGridSizeX / 2);
	let centerOffsetY = $derived(totalGridSizeY / 2);

	function calculatePosition(xi, yi) {
		const x = xi * gridSpacingX - centerOffsetX + 500;
		const y = yi * gridSpacingY - centerOffsetY + 500;

		return { x: x, y: y };
	}

	let huemin = $state(222);
	let huemax = $state(360);
	let lightnessmin = $state(0.13);
	let lightnessmax = $state(0.63);
	let chromamin = $state(0.17);
	let chromamax = $state(0.31);

	// Color amount selection (2-7 colors)
	let selectedColorCount = $state(7);
	let colorOptions = [2, 3, 4, 5, 6, 7];

	// Preset functions
	function applyPreset1() {
		squareCount = 8;
		length = 1;
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
		squareCount = 13;
		length = 0.6;
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
		length = 1.2;
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
			const factor = i / 6;
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
		viewBox="0 0 1000 1000"
		class="svg-canvas"
		shape-rendering="crispEdges"
		style={'background-color: ' + color1}
	>
		<g transform="rotate({Rotation} 500 500)">
			{#each Array(squareCount + 30) as _, yi}
				{#each Array(squareCount + 30) as _, xi}
					{@const isEven = (xi + yi) % 2 === 0}
					{@const poly1Fill =
						selectedColorCount === 4 ? (isEven ? color4 : color2) : isEven ? color2 : color5}
					{@const rect1Fill =
						selectedColorCount === 4 ? (isEven ? color3 : color4) : isEven ? color3 : color6}
					{@const rect2Fill =
						selectedColorCount === 4 ? (isEven ? color2 : color3) : isEven ? color4 : color7}
					{@const poly2Fill =
						selectedColorCount === 4 ? (isEven ? color4 : color2) : isEven ? color5 : color2}
					<g
						transform="translate({calculatePosition(xi, yi).x +
							((xi + yi) % 2 === 0 ? 0 : gridSpacingX) -
							gridSpacingX} {calculatePosition(xi, yi).y}) scale(1, {(xi + yi) % 2 === 0 ? 1 : -1})"
					>
						<polygon
							points={Parallelogramm}
							fill={poly1Fill}
							stroke={poly1Fill}
							stroke-width="0.5"
						/>

						<rect
							transform="translate({cosSize} {sinSize})"
							width={squareSize}
							height={squareSize}
							fill={rect1Fill}
							stroke={rect1Fill}
							stroke-width="0.5"
						/>

						<rect
							transform="translate({cosSize} {sinSize + squareSize})"
							width={squareSize}
							height={squareSize}
							fill={rect2Fill}
							stroke={rect2Fill}
							stroke-width="0.5"
						/>
						<polygon
							points={Parallelogramm2}
							fill={poly2Fill}
							stroke={poly2Fill}
							stroke-width="0.2"
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

	<Slider min={2} max={20} bind:value={squareCount} label="Size" />
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
		color: #fff;
	}

	.preset-btn:active {
		background: #666;
		color: #fff;
		border: 1px solid #777;
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
