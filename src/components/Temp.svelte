<script lang="ts">

	import { onMount } from 'svelte';
	import * as d3 from 'd3';
	import {json} from 'd3'
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

	

	 
 // draw map
 	onMount(async ()=> {

		const us = await fetch('https://raw.githubusercontent.com/dskong07/dsc106-US-education/main/us-states.json')
			.then(d => d.json())
		dataset = us.features
		console.log({ dataset })

		const wb = await json('https://raw.githubusercontent.com/dskong07/dsc106-US-education/main/gendery2.json')
    	grade2 = wb
    	console.log(wb);

		var genders = ["Overall", "Male", "Female"]
        var gen = await d3.select('#gender')

		var myColor = d3.scaleLinear().domain([0,10000]).range(['white','blue'])
        
		gen
        .selectAll('myOptions')
            .data(genders)
        .enter()
        .append('option')
        .text(function (d) { return d; })
        .attr("value", function (d) { return d; })

		var svg = await d3.select("#temp").append("svg").attr("viewBox", "0 0 900 610")


		function update(ddd){
			var u = svg.selectAll("path")
    			.data(ddd)
			u
				.enter()
				.append("path")
				.attr("in:draw", "{{ delay: 0, duration: 1000 }}")
				.attr("class","state")
				.attr("d", path)
				.attr("fill", function (d){
					return myColor(grade2[d.properties.name].Total);
				})
				.attr('stroke', 'black')
				
		}
		
			
		console.log(dataset)
        
		update(dataset)
	


    }
	)
 
 // add labels


    

	
	
</script>

<section class="dropdowns">
    <h3>
        Gender
    </h3>
    <select id="gender"></select>
</section>

<div id="temp">

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