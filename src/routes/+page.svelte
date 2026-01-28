<script>
	import Header from '$lib/components/Header.svelte';
	import { slide } from 'svelte/transition';
	//import Pattern21 from '$lib/components/1_PatternRect.svelte';
	//import Pattern28 from '$lib/components/2_PatternRectStruct.svelte';
	//import Pattern3 from '$lib/components/2-5_polygonnightmare.svelte';
	//import Pattern4 from '$lib/components/3_Quader.svelte';
	//import Pattern5 from '$lib/components/3-5_lustigerQuader.svelte';
	//import Pattern6 from '$lib/components/4_Meinpattern.svelte';
	//import Pattern7 from '$lib/components/5_ColorSwitch.svelte';
	//import Pattern8 from '$lib/components/6_Rotate.svelte';
	//import Pattern9 from '$lib/components/7_ColorScale.svelte';
	//import Pattern13 from '$lib/components/8_RotateColor.svelte';
	//import Pattern11 from '$lib/components/9_Mathecore.svelte';
	//import Pattern15 from '$lib/components/10_Größe.svelte';
	//import Pattern13 from '$lib/components/11_ScalingTest.svelte';
	//import Pattern14 from '$lib/components/12_RhombusCombi.svelte';
	//import Pattern15 from '$lib/components/13_zweiRechnungen.svelte';
	//import Pattern13 from '$lib/components/14_ScalingTestV2.svelte';
	//import Pattern12 from '$lib/components/15_NeuesMuster.svelte';
	//import Pattern11 from '$lib/components/16_LuckyAccident.svelte';
	//import Pattern20 from '$lib/components/17_Colors.svelte';
	//import Pattern21 from '$lib/components/18_UI.svelte';
	//import Pattern22 from '$lib/components/19_ScaleCorrect.svelte';
	//import Pattern23 from '$lib/components/20_moreUI.svelte';
	//import ColorPicker from '$lib/components/21_colorPicker.svelte';
	//import Pattern10 from '$lib/components/22_RangeVariant.svelte';
	//import Pattern9 from '$lib/components/23_NewOptions.svelte';
	//import Pattern8 from '$lib/components/24_BennisZeug.svelte';
	//import Pattern7 from '$lib/components/25_colorAmount.svelte';
	//import fertigesUI from '$lib/components/26_evenmoreUI.svelte';
	//import Pattern23 from '$lib/components/27_hhhha.svelte';
	//import Pattern26 from '$lib/components/28_cleanup.svelte';
	//import Pattern27 from '$lib/components/29_NeuVersuch.svelte';
	//import Pattern28 from '$lib/components/30_drittesMuster.svelte';
	//import ColorVariation from '$lib/components/31_cleanupColor.svelte';
	//import ZoomAlternative from '$lib/components/32_zoomAlternative.svelte';
	//import ColorPicker2 from '$lib/components/33_ColorPicker2.svelte';
	import Pattern29 from '$lib/components/34_ErstesMusterFinal.svelte';
	import Pattern30 from '$lib/components/37_ZweitesMusterFinal.svelte';
	import Pattern31 from '$lib/components/35_ColorVariantFinal.svelte';
	import Pattern32 from '$lib/components/36_DrittesMusterFinal.svelte';
	import Pattern33 from '$lib/components/38_ColorPickerFinal.svelte';

	let patterns = [
		{
			name: 'Muster1',
			component: Pattern29,
			description: 'A grid of squares divided into two triangles that can get an offset.'
		},
		{
			name: 'Muster1 Farbvariante',
			component: Pattern31,
			description: 'A grid of squares divided into two triangles that can get an offset and size.'
		},
		{
			name: 'Muster1 ColorPicker',
			component: Pattern33,
			description: 'A grid of squares divided into two triangles that can get an offset and size.'
		},
		{
			name: 'Muster2',
			component: Pattern30,
			description: 'A grid of squares divided into two triangles that can get an offset and size.'
		},
		{
			name: 'Muster3',
			component: Pattern32,
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
