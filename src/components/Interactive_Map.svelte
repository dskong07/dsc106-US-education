<script>

	import { onMount } from 'svelte';
	import * as d3 from 'd3';
	import {json, scaleLinear, scaleSequential,interpolateRgbBasis} from 'd3'
	import { geoPath, geoAlbersUsa } from 'd3-geo';
	import { draw } from 'svelte/transition';
	export let dataset = [];

	export let elem_total=[];
	export let middle_total=[];
	export let high_total=[];
	export let elem_f=[];
	export let middle_f=[];
	export let high_f=[];
	export let elem_m=[];
	export let middle_m=[];
	export let high_m=[];
	export let allyrs_total=[];
	export let allyrs_f=[];
	export let allyrs_m=[];
	export let curr_dataset;
	let state_statistic = {};
	let total_statistic ={};
	const projection = geoAlbersUsa()
	const path = geoPath(projection)

	 /** list of option for gender dropdown */ 
	 var genders = ["total", "Male", "Female"];

	/** list of option for race dropdown */ 
	var race = ["total", "native", "asian", "hisp", "black", "white", "pcf_isl", "mixed"];

	/** list of option for year dropdown */ 
	var year = ["All", "Elementary", "Middle", "High"];

	 /** user interaction through mouse */ 
	let clicked = -1;
	let selected ={
		properties:{
			name:'50 states, District of Columbia, and Puerto Rico'
		}
	};
	let all_options = 1;
	let gender_selected = 'total'
	let race_selected = 'total'
	let grades_selected = 'All'
	let hovered = -1; 
	let recorded_mouse_position = {
		x: 0, y: 0
	};
	
	let gettinstuff;
	gettinstuff = async () => {
		const at = await json('https://raw.githubusercontent.com/dskong07/dsc106-US-education/main/allyrs_total.json')
    	allyrs_total = at

		const us = await fetch('https://raw.githubusercontent.com/dskong07/dsc106-US-education/main/us-states.json')
			.then(d => d.json())
		dataset = us.features

		return dataset
	}

	let getting_selections;
	getting_selections = async () => {
		selected_race ='total';
		selected_gender='total';
		selected_grade='All';

		return
	}
	
  	onMount( async () => {
		

		//***************************************   ALL GRADES   ***************************************
		const at = await json('https://raw.githubusercontent.com/dskong07/dsc106-US-education/main/allyrs_total.json')
    	allyrs_total = at
		console.log('everyone set')
    	console.log(at);

		//all grades, female
		const af = await json('https://raw.githubusercontent.com/dskong07/dsc106-US-education/main/allyrs_f.json')
    	allyrs_f = af
    	

		//all grades, male
		const am = await json('https://raw.githubusercontent.com/dskong07/dsc106-US-education/main/allyrs_m.json')
    	allyrs_m = am
    	
		//***************************************   ALL GENDERS   ***************************************
		//elementary, total
		const et = await json('https://raw.githubusercontent.com/dskong07/dsc106-US-education/main/elem_total.json')
    	elem_total = et

		//middle, total
		const mt = await json('https://raw.githubusercontent.com/dskong07/dsc106-US-education/main/middle_total.json')
    	middle_total = mt

		//high, total
		const ht = await json('https://raw.githubusercontent.com/dskong07/dsc106-US-education/main/high_total.json')
    	high_total = ht
		
		//***************************************   ALL FEMALE   ***************************************
		//elementary, female
		const ef = await json('https://raw.githubusercontent.com/dskong07/dsc106-US-education/main/elem_f.json')
    	elem_f = ef

		//middle, female
		const mf = await json('https://raw.githubusercontent.com/dskong07/dsc106-US-education/main/middle_f.json')
    	middle_f = mf

		const hf = await json('https://raw.githubusercontent.com/dskong07/dsc106-US-education/main/high_f.json')
    	high_f = hf
		
		//***************************************   ALL MALE   ***************************************
		//elementary, male
		const em = await json('https://raw.githubusercontent.com/dskong07/dsc106-US-education/main/elem_m.json')
    	elem_m = em

		//middle, male
		const mm = await json('https://raw.githubusercontent.com/dskong07/dsc106-US-education/main/middle_m.json')
    	middle_m = mm

		//high, male
		const hm = await json('https://raw.githubusercontent.com/dskong07/dsc106-US-education/main/high_m.json')
    	high_m = hm


		/**geo dataset for drawing base paths*/
		const us = await fetch('https://raw.githubusercontent.com/dskong07/dsc106-US-education/main/us-states.json')
			.then(d => d.json())
		dataset = us.features
		console.log('geojson set')
		console.log({ dataset })

		let current_student_dataset = allyrs_total;
		let selected_race ='total';
		let selected_gender='total';
		let selected_grade='All';

		/**select active dataset*/
		function pick_data(){
			
			if(selected_grade==='All'){
				
				if(selected_gender==="Male"){
					console.log('male')
					current_student_dataset = allyrs_m
				}
				else if(selected_gender==="Female"){
					console.log('female')
					current_student_dataset = allyrs_f
				}
				else{
					current_student_dataset = allyrs_total
				}
			}

			else if(selected_grade==='Elementary'){

				current_student_dataset = elem_total
				if(selected_gender==="Male"){
					current_student_dataset = elem_m
				}
				else if(selected_gender==="Female"){
					current_student_dataset = elem_f
				}
				
			}
			else if(selected_grade==='Middle'){

				current_student_dataset = middle_total
				if(selected_gender==="Male"){
					current_student_dataset = middle_m
				}
				else if(selected_gender==="Female"){
					current_student_dataset = middle_f
				}
			}
			else{

				current_student_dataset = high_total
				if(selected_gender==="Male"){
					current_student_dataset = high_m
				}
				else if(selected_gender==="Female"){
					current_student_dataset = high_f
				}
			}
			console.log('heres the current dataset!')
			console.log(current_student_dataset)
			update(current_student_dataset)
		}
		

		

		/**Take the selected option and update the app */
		

		function update(curr_dataset){
			var x = scaleLinear().domain([0,1]).range(['white','purple'])
			all_options = -1
			gender_selected = selected_gender
			race_selected = selected_race
			grades_selected = selected_grade
			
			if (selected_gender==="Female"){
				
				if(selected_grade==='All'&&selected_race==='total'){
				x = d3.scaleDiverging(["red", "white", "blue"]);
				}
				else{
					x = scaleLinear().domain([0,1]).range(['white','red'])
				}
			}
			else if (selected_gender==="Male"){
				if(selected_grade==='All'&&selected_race==='total'){
				x = d3.scaleDiverging(["red", "white", "blue"]);
				}
				else{
					x = scaleLinear().domain([0,1]).range(['white','blue'])
				}
			}


			var altcolor = x 

			console.log("updating...")
			var states = d3.select("#mappy").selectAll("path").data(dataset)
			states.enter()

			var curr_race = 'total'
			var denom = curr_dataset

			if(selected_gender==='total'){
				if (selected_race==='total'){
					if(selected_grade!=='All'){
						denom = allyrs_total
						all_options = 1
						console.log('me!')
					}

				}
			}

			else if(selected_gender!=='total'){
				if(selected_race==='total'){
					denom = allyrs_total
				}
			}

			else{

			}
			state_statistic={}
			states
			.transition()
			.delay(20)
			.duration(1000)
			.attr("fill", function(d){
				if(selected_gender==='total' && selected_grade==='All' && selected_race==='total'){
					altcolor = d3.scaleLinear().domain([0,80000]).range(['white','purple'])
					return altcolor(curr_dataset[d.properties.name][selected_race])
				}
				console.log(curr_dataset[d.properties.name][selected_race]/denom[d.properties.name][curr_race])
				state_statistic[d.properties.name] = curr_dataset[d.properties.name][selected_race]/denom[d.properties.name][curr_race]
				return altcolor(curr_dataset[d.properties.name][selected_race]/denom[d.properties.name][curr_race])
			})
			console.log(state_statistic)
		}
		

		 /** gen is the button itself ---------------------------------------------------------*/ 
		var gen = await d3.select('#gender')
        gen
        .selectAll('myOptions')
            .data(genders)
        .enter()
        .append('option')
        .text(function (d) { return d; })
        .attr("value", function (d) { return d; })

		/**When the selection change, we record what  is being selected*/
		gen.on("change", function(d) {
			selected_gender = d3.select(this).property("value")
			pick_data();
		}
		)


		/** rac is the button itself -----------------------------------------------------------*/ 
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
			selected_race = d3.select(this).property("value")
			pick_data();
		}
		)
		
		/** gra is the button itself ---------------------------------------------------------------*/ 
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
			selected_grade = d3.select(this).property("value")
			pick_data();

			
			
		}
		)

		function reset_filters(){
			console.log('resetting filters!')
			selected_gender = 'total'
			selected_grade = 'All'
			selected_race = 'total'
			pick_data();
		}

		var reset_button = await d3.select('#resetb')

		reset_button
		.on('click',() =>{
			var first = document.getElementById("gender");
			var sec = document.getElementById("race");
			var thir = document.getElementById("year");
			first.selectedIndex=0;
			sec.selectedIndex=0;
			thir.selectedIndex=0;
			console.log('click')
			reset_filters();
			all_options = 1;

		})
		

	}
	)

	
	var myColor = d3.scaleLinear().domain([0,80000]).range(['white','purple'])
	

	
</script>

<!--visualization for the map!  -->
<div class="visualization">
    <svg id="mappy" viewBox="0 0 900 610">
        <g fill="white" stroke="black">
			{#await gettinstuff()}
			{:then dataset}
				{#each dataset as feature,i}
					{total_statistic[feature.properties.name] = allyrs_total[feature.properties.name].total}
					{all_options = 1}
					<path 
					d={path(feature)} 
					fill={myColor(allyrs_total[feature.properties.name].total)} 
					on:click={() => {selected = feature; clicked = 1}} 
					on:mouseover={(event) =>{
						hovered = i; recorded_mouse_position = {
							x: event.pageX,
							y: event.pageY
						}
						//console.log(i, recorded_mouse_position)
					}}
					on:mouseout={(event) => { hovered = -1; }}
					class="state"
					
					in:draw={{ delay: 0, duration: 1000 }} />
				{/each}
			{/await}
            
        </g>		
        {#if selected}
            <path d={path(selected)} fill="hsl(0 5% 50% / 60%)" stroke="black" stroke-width={2} />
        {/if}
    </svg>
</div>


<div class = "filtering">
	<h2>
		Filter Categories
	</h2>
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

	<button class = reset_button id='resetb'>
		reset filters
	</button>


</div>
<div class="tooltip-selected">
	
	{#if clicked !== -1}
		{#if gender_selected === "total" && race_selected === "total" && grades_selected === 'All'}
			There were {total_statistic[selected.properties.name]} students held back in the state of {selected.properties.name}.
		{:else if gender_selected === "total" && race_selected === "total" && grades_selected !== 'All'}
			Out of all students held back in {selected.properties.name}, {Math.round(state_statistic[selected.properties.name]* 1000)/10}% are {grades_selected} school students.
		{:else if gender_selected === "total" && race_selected !== "total" && grades_selected === 'All'}
			Out of all students held back in {selected.properties.name}, {Math.round(state_statistic[selected.properties.name]* 1000)/10}% are {race_selected} students.
		{:else if gender_selected === "total" && race_selected !== "total" && grades_selected !== 'All'}
			Out of all {grades_selected} School students held back in {selected.properties.name}, {Math.round(state_statistic[selected.properties.name]* 1000)/10}% are {race_selected} students.
		{:else if gender_selected !== "total" && race_selected === "total" && grades_selected === 'All'}
			Out of all students held back in {selected.properties.name}, {Math.round(state_statistic[selected.properties.name]* 1000)/10}% are {gender_selected} students.
		{:else if gender_selected !== "total" && race_selected === "total" && grades_selected !== 'All'}
			Out of all {gender_selected} students held back in {selected.properties.name}, {Math.round(state_statistic[selected.properties.name]* 1000)/10}% were {grades_selected} School students .
		{:else if gender_selected !== "total" && race_selected !== "total" && grades_selected === 'All'}
			Out of all {gender_selected} students held back in {selected.properties.name}, {Math.round(state_statistic[selected.properties.name]* 1000)/10}% were {race_selected} students.
		{:else if gender_selected !== "total" && race_selected !== "total" && grades_selected !== 'All'}
			Out of all {gender_selected}, {grades_selected} School students held back in {selected.properties.name}, {Math.round(state_statistic[selected.properties.name]* 1000)/10}% were {race_selected} students.
		{/if}
		<!-- 
		Selected: {selected.properties.name}
		had {grade2[selected.properties.name].Total}
		second year students held back in 2017-2018! -->
	{:else}
		{#if gender_selected === "total" && race_selected === "total" && grades_selected === 'All'}
			Hover over a state to see the number of students held back.
			<br>Select the state for a more detailed explanation of the visualization!
		{:else if gender_selected === "total" && race_selected === "total" && grades_selected !== 'All'}
			Hover over the state to see the percent of {grades_selected} School students held back out of all the held back student in selected state
			<br>Select the state for a more detailed explanation of the visualization!
		{:else if gender_selected === "total" && race_selected !== "total" && grades_selected === 'All'}
			Hover over the state to see the percent of {race_selected} students held back out of all the held back student in selected state
			<br>Select the state for a more detailed explanation of the visualization!
		{:else if gender_selected === "total" && race_selected !== "total" && grades_selected !== 'All'}
			Hover over the state to see the percent of {race_selected} students held back out of {grades_selected} School students that are held back in each selected state
			<br>Select the state for a more detailed explanation of the visualization!
		{:else if gender_selected !== "total" && race_selected === "total" && grades_selected === 'All'}
			Hover over the state to see the percent of {gender_selected} students held back in each selected state
			<br>Select the state for a more detailed explanation of the visualization!
		{:else if gender_selected !== "total" && race_selected === "total" && grades_selected !== 'All'}
			Hover over the state to see the percent of {grades_selected} School students held back out of all the {gender_selected} students that are held back in each selected state
			<br>Select the state for a more detailed explanation of the visualization!
		{:else if gender_selected !== "total" && race_selected !== "total" && grades_selected === 'All'}
			Hover over the state to see the percent of {gender_selected}, {race_selected} students held back out of all the {gender_selected} students that are held back in each selected state
			<br>Select the state for a more detailed explanation of the visualization!
		{:else if gender_selected !== "total" && race_selected !== "total" && grades_selected !== 'All'}
			Hover over the state to see the percent of {race_selected} students held back out of all the {gender_selected}, {grades_selected} School students that are held back in each selected state
			<br>Select the state for a more detailed explanation of the visualization!
		{/if}
	{/if}
</div>

<div	
class={hovered === -1 ? "tooltip-hidden": "tooltip-visible"}
style="left: {recorded_mouse_position.x + 40}px; top: {recorded_mouse_position.y + 40}px">

		{#if hovered !== -1}
			{#if gender_selected === "total" && race_selected === "total" && grades_selected === 'All'}
				Selected state: {dataset[hovered].properties.name}
				<br><br>Number of students held back: {total_statistic[dataset[hovered].properties.name]}
			{:else if gender_selected === "total" && race_selected === "total" && grades_selected !== 'All'}
				Selected state: {dataset[hovered].properties.name}
				<br><br>Proportion of held back students in {grades_selected} Schooler: {Math.round(state_statistic[dataset[hovered].properties.name] * 1000)/10}% 
			{:else if gender_selected === "total" && race_selected !== "total" && grades_selected === 'All'}
				Selected state: {dataset[hovered].properties.name}
				<br><br>Proportion of held back students who were {race_selected}: {Math.round(state_statistic[dataset[hovered].properties.name] * 1000)/10}%
			{:else if gender_selected === "total" && race_selected !== "total" && grades_selected !== 'All'}
				Selected state: {dataset[hovered].properties.name}
				<br><br>Proportion of {grades_selected} School students held back who were {race_selected}: {Math.round(state_statistic[dataset[hovered].properties.name] * 1000)/10}%
			{:else if gender_selected !== "total" && race_selected === "total" && grades_selected === 'All'}
				Selected state: {dataset[hovered].properties.name}
				<br><br>Proportion of held back student who were {gender_selected}: {Math.round(state_statistic[dataset[hovered].properties.name] * 1000)/10}%
			{:else if gender_selected !== "total" && race_selected === "total" && grades_selected !== 'All'}
				Selected state: {dataset[hovered].properties.name}
				<br><br>Proportion of {gender_selected} {grades_selected} schooler students held back: {Math.round(state_statistic[dataset[hovered].properties.name] * 1000)/10}%
			{:else if gender_selected !== "total" && race_selected !== "total" && grades_selected === 'All'}
				Selected state: {dataset[hovered].properties.name}
				<br><br>Proportion of {gender_selected} students held back who were {race_selected}: {Math.round(state_statistic[dataset[hovered].properties.name] * 1000)/10}%
			{:else if gender_selected !== "total" && race_selected !== "total" && grades_selected !== 'All'}
				Selected state: {dataset[hovered].properties.name}
				<br><br>Proportion of {gender_selected}, {grades_selected} School students held back who were {race_selected}: {Math.round(state_statistic[dataset[hovered].properties.name] * 1000)/10}%
			{/if}
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
		transform: translateY(-500px);
		height: 400px;
		width: 700px;

	}
	.tooltip-hidden {
		visibility: hidden;
		font-family: "Nunito", sans-serif;
		width: 200px;
		position: absolute;
	}

	.tooltip-visible {
		font: 20px sans-serif;
		font-family: "Nunito", sans-serif;
		visibility: visible;
		background-color: #f0dba8;
		border-radius: 10px;
		width: 200px;
		color: black;
		position: absolute;
		padding: 10px;
	}

	.reset_button {
    /* Your button styles */

    padding: 20px 20px;
	margin: 40px;
    color: #696262; 
    border: none;
    border-radius: 5px;
    cursor: pointer;
	}
	.filtering{
		width: 300px;
		height: 500px;
		transform: translate(-500px, -500px);
		font-family: 'Roboto', sans-serif;
	}

	.dropdowns{
        font-family: 'Roboto', sans-serif;
	}

</style>

<!--
if gender IS total
	if race IS total
		if grade IS total
			<none>     <-------------- COMPARING STATE vs. WHOLE COUNTRY (e.x - what percentage of dropouts in texas out of ALL grades in ALL states)
		else (if grade NOT total)
			<grade>    <-------------- COMPARING GRADE IN THAT STATE vs. ALL GRADES IN THAT STATE (e.x percentage of dropouts in texas in highschool out of ALL GRADES in TEXAS, all races and genders included)
	else (if race NOT total)
		if grade IS total
			<race>     <-------------- COMPARING RACE IN THAT STATE vs. ALL RACES IN THAT STATE (e.x percentage of dropouts in texas who are asian out of ALL races in TEXAS, all grades and genders included)
		else (grade NOT total)
			<race and grade>    <-------- COMPARING RACE AND GRADE IN THAT STATE vs. ALL RACES IN THAT GRADE FROM STATE (e.x percentage of asian dropouts in texas in highschool out of ALL races in highschool in TEXAS)

else if (gender NOT total)
	if race IS total
		if grade IS total
			<gender>   <-------------- COMPARING GENDER IN THAT STATE vs. ALL GENDERS IN THAT STATE (e.x percentage of male dropouts in texas out of ALL genders in TEXAS, all races and grades included)
		else (if grade NOT total)
			<gender and grade>  <----- COMPARING GENDER AND GRADE IN THAT STATE vs. ALL GRADES IN THAT STATE OF THAT GENDER(e.x percentage of male highschool dropouts in texas out of males of ALL grades in TEXAS, all races included)
	else (if race NOT total)
		if grade IS total
			<gender and race>    <-------- COMPARING GENDER AND RACE IN THAT STATE vs. ALL RACES IN THAT STATE (e.x percentage of male asian dropouts in texas out of ALL male dropouts in TEXAS, all races included)
		else (grade NOT total)
			<gender and race and grade>  <----- COMPARING GRADE GENDER AND RACE IN THAT STATE vs. ALL RACES OF THAT GENDER AND GRADE IN STATE(e.x percentage of highschool asian male dropouts in texas in out of ALL highschool male dropouts in TEXAS)


-->