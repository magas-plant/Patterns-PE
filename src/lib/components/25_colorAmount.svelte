<script>
	import chroma from 'chroma-js';
	import Slider from '$lib/ui/Slider.svelte';
	import RangeSlider from '$lib/ui/RangeSlider.svelte';

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

	let gridSpacing = $derived(squareSize + cosSize + offset);
	let totalGridSize = $derived((squareCount + 25) * gridSpacing);
	let centerOffset = $derived(totalGridSize / 2);

	function calculatePosition(xi, yi) {
		const x = xi * gridSpacing - centerOffset + 500;
		const y = yi * gridSpacing - centerOffset + 500;

		return { x: x, y: y };
	}

	let huemin = $state(0);
	let huemax = $state(360);
	let lightnessmin = $state(0.5);
	let lightnessmax = $state(0.8);
	let chromamin = $state(0.2);
	let chromamax = $state(0.5);

	// Color amount selection (2-7 colors)
	let selectedColorCount = $state(4); // Default: 4 colors
	let colorOptions = [2, 3, 4, 5, 6, 7];

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
	let color1 = $derived(activeColors[0]); // Background
	let color2 = $derived(activeColors[Math.min(1, activeColors.length - 1)]);
	let color3 = $derived(activeColors[Math.min(2, activeColors.length - 1)]);
	let color4 = $derived(activeColors[Math.min(3, activeColors.length - 1)]);
	let color5 = $derived(activeColors[Math.min(4, activeColors.length - 1)]);
	let color6 = $derived(activeColors[Math.min(5, activeColors.length - 1)]);
	let color7 = $derived(activeColors[Math.min(6, activeColors.length - 1)]);
</script>

<div class="sidebar">
	<h3 style="margin: 0 0 10px 0; font-size: 1rem; font-weight: 500;">Pattern Controls</h3>

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
		<label style="font-size: 0.875rem; color: #666; margin-bottom: 8px; display: block;"
			>Number of Colors (2-7)</label
		>
		<div class="checkbox-group">
			{#each colorOptions as count}
				<label class="checkbox-label">
					<input
						type="radio"
						name="colorCount"
						value={count}
						checked={selectedColorCount === count}
						onchange={() => (selectedColorCount = count)}
					/>
					<span>{count}</span>
				</label>
			{/each}
		</div>
	</div>
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
						/>
						<polygon points={Parallelogramm2} fill={poly2Fill} />
						<rect
							transform="translate({cosSize} 0)"
							width={squareSize}
							height={squareSize}
							fill={rectFill}
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

	.color-amount-section {
		display: flex;
		flex-direction: column;
	}

	.checkbox-group {
		display: flex;
		flex-wrap: wrap;
		gap: 10px;
	}

	.checkbox-label {
		display: flex;
		align-items: center;
		gap: 5px;
		cursor: pointer;
		font-size: 0.875rem;
		color: #333;
	}

	.checkbox-label input[type='radio'] {
		cursor: pointer;
		width: 16px;
		height: 16px;
	}

	.checkbox-label span {
		user-select: none;
	}
</style>
