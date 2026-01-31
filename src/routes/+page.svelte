<script>
	import Header from '$lib/components/Header.svelte';
	import { slide } from 'svelte/transition';
	//import Pattern1 from '$lib/components/process/1_PatternRect.svelte';
	//import Pattern2 from '$lib/components/process/2_PatternRectStruct.svelte';
	//import Pattern3 from '$lib/components/process/2-5_polygonnightmare.svelte';
	//import Pattern4 from '$lib/components/process/3_Quader.svelte';
	//import Pattern5 from '$lib/components/process/3-5_lustigerQuader.svelte';
	//import Pattern6 from '$lib/components/process/4_Meinpattern.svelte';
	//import Pattern7 from '$lib/components/process/5_ColorSwitch.svelte';
	//import Pattern8 from '$lib/components/process/6_Rotate.svelte';
	//import Pattern9 from '$lib/components/process/7_ColorScale.svelte';
	//import Pattern10 from '$lib/components/process/8_RotateColor.svelte';
	//import Pattern11 from '$lib/components/process/9_Mathecore.svelte';
	//import Pattern12 from '$lib/components/process/10_Größe.svelte';
	//import Pattern13 from '$lib/components/process/11_ScalingTest.svelte';
	//import Pattern14 from '$lib/components/process/12_RhombusCombi.svelte';
	//import Pattern15 from '$lib/components/process/13_zweiRechnungen.svelte';
	//import Pattern16 from '$lib/components/process/14_ScalingTestV2.svelte';
	//import Pattern17 from '$lib/components/process/15_NeuesMuster.svelte';
	//import Pattern18 from '$lib/components/process/16_LuckyAccident.svelte';
	//import Pattern19 from '$lib/components/process/17_Colors.svelte';
	//import Pattern20 from '$lib/components/process/18_UI.svelte';
	//import Pattern21 from '$lib/components/process/19_ScaleCorrect.svelte';
	//import Pattern22 from '$lib/components/process/20_moreUI.svelte';
	//import Pattern23 from '$lib/components/process/21_colorPicker.svelte';
	//import Pattern24 from '$lib/components/process/22_RangeVariant.svelte';
	//import Pattern25 from '$lib/components/process/23_NewOptions.svelte';
	//import Pattern26 from '$lib/components/process/24_BennisZeug.svelte';
	//import Pattern27 from '$lib/components/process/25_colorAmount.svelte';
	//import Pattern28 from '$lib/components/process/26_evenmoreUI.svelte';
	//import Pattern29 from '$lib/components/process/27_hhhha.svelte';
	//import Pattern30 from '$lib/components/process/28_cleanup.svelte';
	//import Pattern31 from '$lib/components/process/29_NeuVersuch.svelte';
	//import Pattern32 from '$lib/components/process/30_drittesMuster.svelte';
	//import Pattern33 from '$lib/components/process/31_cleanupColor.svelte';
	//import Pattern34 from '$lib/components/process/32_zoomAlternative.svelte';
	//import Pattern35 from '$lib/components/process/33_ColorPicker2.svelte';
	import Pattern36 from '$lib/components/34_ErstesMusterFinal.svelte';
	import Pattern37 from '$lib/components/37_ZweitesMusterFinal.svelte';
	import Pattern38 from '$lib/components/35_ColorVariantFinal.svelte';
	import Pattern39 from '$lib/components/36_DrittesMusterFinal.svelte';
	import Pattern40 from '$lib/components/38_ColorPickerFinal.svelte';
	//import Pattern41 from '$lib/components/process/39_backgroundGenerator.svelte';
	import Pattern43 from '$lib/components/40_BaseColorVariant.svelte';

	let patterns = [
		{
			name: 'Square Expansion',
			component: Pattern36,
			description: 'A grid of squares divided into two triangles that can get an offset.'
		},
		{
			name: 'Square Expansion Color Variant 1',
			component: Pattern38,
			description: 'A grid of squares divided into two triangles that can get an offset and size.'
		},
		{
			name: 'Square Expansion Color Variant 2',
			component: Pattern40,
			description: 'A grid of squares divided into two triangles that can get an offset and size.'
		},
		{
			name: 'Square Expansion Color Variant 3',
			component: Pattern43,
			description: 'Generates a colorful background pattern based on various parameters.'
		},
		{
			name: 'Square Expansion x2',
			component: Pattern37,
			description: 'A grid of squares divided into two triangles that can get an offset and size.'
		},
		{
			name: 'Square Expansion ^2',
			component: Pattern39,
			description: 'A grid of squares divided into two triangles that can get an offset and size.'
		}
	];

	let selectedPattern = $state(0);
	let SelectedPattern = $derived(patterns[selectedPattern].component);
</script>

<div class="app-container">
	<Header />
	<main class="app-main">
		<div class="sidebar-left">
			{#each patterns as pattern, index}
				<button
					class="sidebar-left-item"
					class:selected={selectedPattern === index}
					onclick={() => (selectedPattern = index)}
					>{pattern.name}
					{#if selectedPattern === index}
						<div transition:slide class="sidebar-left-description">{pattern.description}</div>
					{/if}
				</button>
			{/each}
		</div>

		<SelectedPattern />
	</main>
</div>
