<script>
    import * as d3 from 'd3';
    import { onMount } from 'svelte';
    export let genders = [];
    export let years;
    export let races=[];

    onMount(async ()=> {
        var gen = await d3.select('#gender')
        gen
        .selectAll('myOptions')
            .data(genders)
        .enter()
        .append('option')
        .text(function (d) { return d; })
        .attr("value", function (d) { return d; })

        var race = await d3.select('#race')
        race
        .selectAll('myOptions')
            .data(years)
        .enter()
        .append('option')
        .text(function (d) { return d; })
        .attr("value", function (d) { return d; })

        var year = await d3.select('#year')
        year
        .selectAll('myOptions')
            .data(races)
        .enter()
        .append('option')
        .text(function (d) { return d; })
        .attr("value", function (d) { return d; })




    }

    )



</script>

<section class="dropdowns">
    <h3>
        Gender
    </h3>
    <select id="gender"></select>
    
    <h3>
        Race
    </h3>
    <select id="race"></select>
    <h3>
        Grade
    </h3>
    <select id="year"></select>
</section>

<section class='reset'>
    <button 
    id='resetb'
    on:click={() => {
        var first = document.getElementById("gender");
        var sec = document.getElementById("race");
        var thir = document.getElementById("year");
        first.selectedIndex=0;
        sec.selectedIndex=0;
        thir.selectedIndex=0;
        console.log('click')
    }}>
        Reset Filter
    </button>
</section>

<style>
    .reset{
        padding-top: 60px;
    }
</style>

