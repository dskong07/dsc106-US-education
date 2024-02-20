<script>

	import { onMount } from 'svelte';
	import * as d3 from 'd3';
	import { geoPath, geoAlbersUsa } from 'd3-geo';
	import { draw } from 'svelte/transition';
	export let dataset = [];
	export let grade2 = [];
	const projection = geoAlbersUsa()	
	const path = geoPath(projection)

	 /** list of option for gender dropdown */ 
	var genders = ["Overall", "Male", "Female"]

	 /** list of option for race dropdown */ 
	 var race = ["Overall", "White", "Asian"]

	 /** list of option for year dropdown */ 
	 var year = ["Overall", "Elementary", "Middle", "High"]

	 /** user interaction through mouse */ 
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
	

  	onMount(async () => {
		const wb = await json('https://raw.githubusercontent.com/dkong07/dsc106-p3/main/gendery2.json')
    	grade2 = wb
    	console.log(wb);
		const us = await fetch('https://raw.githubusercontent.com/dkong07/dsc106-p3/main/us-states.json')
			.then(d => d.json())
		dataset = us.features
		console.log({ dataset })
		
		 /** gen is the button itself */ 
		var gen = await d3.select('#gender')
        gen
        .selectAll('myOptions')
            .data(genders)
        .enter()
        .append('option')
        .text(function (d) { return d; })
        .attr("value", function (d) { return d; })

		/**Take the selected option and update the app */
		function update(gen){
			var altcolor = d3.scaleLinear().domain([0,grade2['50 states, District of Columbia, and Puerto Rico'][gen]/57]).range(['white','red'])
			if (gen==='Overall'){
				gen = "Total"
				var altcolor = d3.scaleLinear().domain([0,5000]).range(['white','blue'])
			}
			console.log(gen)
			/**select the map container and all the path*/
			var states = d3.select("#mappy").selectAll("path").data(dataset)
			states.enter()
			console.log("here")
			states
			.transition()
			.delay(20)
			.duration(1000)
			/**d.properties.name refer to the state*/
			.attr("fill", function(d){
				return altcolor(grade2[d.properties.name][gen])
			})
			
		}
		/**When the selection change, we record what  is being selected*/
		gen.on("change", function(d) {
			var selectedOption = d3.select(this).property("value")
			update(selectedOption)
		}
		)

		 /** rac is the button itself */ 
		 var rac = await d3.select('#race')
		 rac
        .selectAll('myOptions')
            .data(race)
        .enter()
        .append('option')
        .text(function (d) { return d; })
        .attr("value", function (d) { return d; })


		/**When the selection change, we record what  is being selected*/
		rac.on("change", function(d) {
			var selectedOption = d3.select(this).property("value")
			update(selectedOption)
		}
		)


		 /** gra is the button itself */ 
		 var gra = await d3.select('#year')
		 gra
        .selectAll('myOptions')
            .data(year)
        .enter()
        .append('option')
        .text(function (d) { return d; })
        .attr("value", function (d) { return d; })


		/**When the selection change, we record what  is being selected*/
		gra.on("change", function(d) {
			var selectedOption = d3.select(this).property("value")
			update(selectedOption)
		}
		)


	})
	
	var myColor = d3.scaleLinear().domain([0,10000]).range(['white','blue'])
	
</script>

<!--visualization for the map! -->
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


<!-- Adding a dropdown for gender-->
<section class="dropdowns">
    <h3>
        Gender
    </h3>
	<!-- This gender is the variable gender pointing at array of options-->
    <select id="gender"></select>
</section>

<!-- Adding a dropdown for race-->
<section class="dropdowns">
    <h3>
        Race
    </h3>
	<!-- This gender is the variable gender pointing at array of options-->
    <select id="race"></select>
</section>

<!-- Adding a dropdown for grade-->
<section class="dropdowns">
    <h3>
        Grades
    </h3>
	<!-- This gender is the variable gender pointing at array of options-->
    <select id="year"></select>
</section>

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