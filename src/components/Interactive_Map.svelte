<script>

	import { onMount } from 'svelte';
	import * as d3 from 'd3';
	import { geoPath, geoAlbersUsa } from 'd3-geo';
	import { draw } from 'svelte/transition';
	export let dataset = [];
	export let grade2 = [];
	const projection = geoAlbersUsa()	
	const path = geoPath(projection)
	let clicked = -1;
	let selected ={
		properties:{
			name:'50 states, District of Columbia, and Puerto Rico'
		}
	};
	let hovered = -1; 
	let recorded_mouse_position = {
		x: 0, y: 0
	};
	
	
	var myColor = d3.scaleLinear().domain([0,10000]).range(['white','blue'])
	
</script>
<div class="visualization">
    <svg viewBox="0 0 1200 610">
        <g fill="white" stroke="black">
            {#each dataset as feature,i}
                <path 
                d={path(feature)} 
                fill={i===hovered ? "hsl(0 0% 50% / 20%)" : myColor(grade2[feature.properties.name].Total)}
                on:click={() => {selected = feature; clicked = 1}} 
                on:mouseover={(event) =>{
                    hovered = i; recorded_mouse_position = {
                        x: event.pageX,
                        y: event.pageY
                    }
                    console.log(i, recorded_mouse_position)
                }}
                on:mouseout={(event) => { hovered = -1; }}
                class="state" 
                in:draw={{ delay: 0, duration: 1000 }} />
            {/each}
        </g>		
        {#if selected}
            <path d={path(selected)} fill="hsl(0 5% 50% / 60%)" stroke="black" stroke-width={2} />
        {/if}
    </svg>
</div>


<div class="tooltip-selected">
	
	
	{#if clicked !== -1}
		Selected: {selected.properties.name}
		had {grade2[selected.properties.name].Total}
		second year students held back in 2017-2018!
	{:else}
		pick a state!
	{/if}
</div>

<div	
class={hovered === -1 ? "tooltip-hidden": "tooltip-visible"}
style="left: {recorded_mouse_position.x + 40}px; top: {recorded_mouse_position.y + 40}px">
	{#if hovered !== -1}
		this be {dataset[hovered].properties.name}
	{/if}
</div>
	
<style>
    .visualization {
        font: 25px sans-serif;
		margin: auto;
		margin-top: 1px;
		text-align: middle;
    }
	.tooltip-selected {
		text-align: center;
		margin-top: 8px;
		font-size: 1.5rem;
	}
	.tooltip-hidden {
		visibility: hidden;
		font-family: "Nunito", sans-serif;
		width: 200px;
		position: absolute;
	}

	.tooltip-visible {
		font: 25px sans-serif;
		font-family: "Nunito", sans-serif;
		visibility: visible;
		background-color: #f0dba8;
		border-radius: 10px;
		width: 200px;
		color: black;
		position: absolute;
		padding: 10px;
	}
</style>

<!---->