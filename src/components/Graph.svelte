<script>
	import * as d3 from 'd3';
	// our interactive data
	export let todo_category = [];
	let arcGenerator = d3.arc()
		.innerRadius(10)
		.outerRadius(100)
		.padAngle(.02)
		.cornerRadius(4);
	
	let pieAngleGenerator = d3.pie().value( d => d[1] );
	let arc_data = []
	
	const arc_color = d3.scaleOrdinal()
		.domain(arc_data.map(d => d.name))
		.range(["#dc7926", " #dcc826", "#9adc26", "#26dc5a" ,"#26cedc"]);
	
	let recorded_mouse_position = {
		x: 0, y: 0
	};

	let hovered = -1; 
	$: {
		// interactive data here
		// input data grouped by category
		// looks like [[category, count], [category, count], [category, count]]
		let todo_count_by_category = d3.rollups(
			todo_category, 
			v => v.length, 
			d => d.category
		);
		
		arc_data = pieAngleGenerator(todo_count_by_category);
		console.log(arc_data)	

	}

</script>

<div class="visualization">
	<svg width="500" height="500">
		<g transform="translate(250, 120)">
			<!-- Place for Pie -->
			{#each arc_data as data, index}
			
			<path 
				d={arcGenerator({
					startAngle: data.startAngle,
					endAngle: data.endAngle
				})}
				fill={index === hovered ? "grey": arc_color(index)}
				on:mouseover={(event) => { 
					hovered = index; recorded_mouse_position = {
					x: event.pageX,
					y: event.pageY
						} 
					}}
				on:mouseout={(event) => { hovered = -1; }}
			/>
			<text
				
				transform="translate({arcGenerator.centroid(data)})"
				text-anchor={"middle"}
			>
			{data.data[0]}
			</text>
			{/each}
		</g>
	</svg>
	<div
	class={hovered === -1 ? "tooltip-hidden": "tooltip-visible"}
	style="left: {recorded_mouse_position.x + 40}px; top: {recorded_mouse_position.y + 40}px"
>
	{#if hovered !== -1}
		There {arc_data[hovered].data[1] === 1 ? "is" : "are"} 
		{arc_data[hovered].data[1]} 
		record{arc_data[hovered].data[1] === 1 ? "" : "s"}
		where you have "{arc_data[hovered].data[0]}" todo items.
	{/if}
</div>


</div>

<style>
	.visualization {
		font: 25px sans-serif;
		margin: auto;
		margin-top: 1px;
		text-align: middle;
	}

	/* dynamic classes for the tooltip */
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