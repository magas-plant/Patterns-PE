<script>
	import chroma from 'chroma-js';
	import Slider from '$lib/ui/Slider.svelte';
	import RangeSlider from '$lib/ui/RangeSlider.svelte';

	let squareCount = $state(9);
	let length = $state(1);

	let cos = Math.cos(Math.PI / 4);
	let squareSize = $derived(1000 / squareCount);
	let widthFactor = $state(1);

	let cosSize = $derived(cos * squareSize * length);

	let Parallelogramm = $derived(
		`0,0 ${squareSize},0 ${squareSize + cosSize},${cosSize} ${cosSize},${cosSize}`
	);
	let Parallelogramm2 = $derived(
		`${cosSize}, ${squareSize} ${cosSize + squareSize}, ${squareSize} ${squareSize}, ${squareSize + cosSize}, 0, ${squareSize + cosSize}`
	);
	let Parallelogramm3 = $derived(
		`${cosSize}, ${squareSize} ${cosSize + squareSize * widthFactor}, ${squareSize} ${squareSize}, ${squareSize + cosSize}, 0, ${squareSize + cosSize}`
	);

	let Rotatepoint = $derived((squareSize + cosSize) / 2);
	let offset = $state(0);

	let Rotation = $state(0);

	function calculatePosition(xi, yi) {
		const x = xi * (squareSize + cosSize + offset) - 500;
		const y = yi * (squareSize + cosSize + offset) - 500;

		return { x: x, y: y };
	}

	let huemin = $state(0);
	let huemax = $state(360);
	let lightnessmin = $state(0.5);
	let lightnessmax = $state(1);
	let chromamin = $state(0.2);
	let chromamax = $state(1);
	let color1 = $derived(chroma.oklch(lightnessmin, chromamin, huemin));
	let color2 = $derived(
		chroma.oklch(
			lightnessmin + (lightnessmax - lightnessmin) / 4,
			chromamin + (chromamax - chromamin) / 4,
			huemin + (huemax - huemin) / 4
		)
	);
	let color3 = $derived(
		chroma.oklch(
			lightnessmin + (lightnessmax - lightnessmin) / 2,
			chromamin + (chromamax - chromamin) / 2,
			huemin + (huemax - huemin) / 2
		)
	);
	let color4 = $derived(chroma.oklch(lightnessmax - 0.4, chromamax, huemax));
</script>

<div class="sidebar">
	<h3 style="margin: 0 0 10px 0; font-size: 1rem; font-weight: 500;">Pattern Controls</h3>

	<Slider min={2} max={30} bind:value={squareCount} label="Size" />
	<Slider min={0} max={10} step="0.1" bind:value={length} label="Length" />
	<Slider min={0} max={90} bind:value={Rotation} label="Rotation" />
	<Slider min={1} max={2} step="0.1" bind:value={widthFactor} label="Width" />

	<RangeSlider min={0} max={360} bind:value1={huemin} bind:value2={huemax} label="Hue Range" />

	<RangeSlider
		min={0}
		max={1}
		step={0.01}
		bind:value1={lightnessmin}
		bind:value2={lightnessmax}
		label="Lightness Range"
	/>

	<RangeSlider
		min={0}
		max={1}
		step={0.01}
		bind:value1={chromamin}
		bind:value2={chromamax}
		label="Chroma Range"
	/>
</div>

<div class="svg-container">
	<svg
		viewBox="0 0 1000 1000"
		class="svg-canvas"
		shape-rendering="crispEdges"
		style={'background-color: ' + color4}
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
						/>
						<polygon
							points={(xi + yi) % 2 === 0 ? Parallelogramm2 : Parallelogramm3}
							fill={(xi + yi) % 2 == 0 ? color3 : color1}
						/>
						<rect
							transform="translate({cosSize} 0)"
							width={squareSize * widthFactor}
							height={squareSize}
							fill={(xi + yi) % 2 == 0 ? color2 : color3}
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
