<script>
  import { json,csv } from "d3";
  
  import { onMount } from 'svelte';
  import { read, utils, writeFile } from 'xlsx';
  import InteractiveMap from "./Interactive_Map.svelte";
  var genders;
  var years = ["All Grades",2,3]
  var races = ['All Races','White','Asian']
  
  let dataset = [];
  onMount(async () => {
		const us = await fetch('https://raw.githubusercontent.com/dskong07/dsc106-US-education/main/us-states.json')
			.then(d => d.json())
		dataset = us.features
		
	})



  let allyrs_total =[];
  onMount(async () => {
    const at = await json('https://raw.githubusercontent.com/dskong07/dsc106-US-education/main/allyrs_total.json')
    allyrs_total = at
    console.log(at);
    
  })

  


</script>

<main class="main">

  <div class='applets'>


    <section class="graph">
      <h2 class = usa style="margin-top: 15px">Map of USA</h2>
      <InteractiveMap {dataset}{allyrs_total}/>
    
    </section>
  </div>
  <!--
  <div class='applets'>
    <section class="filter">
      <h2>
        Filter By:
      </h2>
      <Filter {genders}{years}{races}/>
      
    </section>
  </div>
  -->
<div class='about'>
  All data from <a href='https://civilrightsdata.ed.gov/estimations/2017-2018'>Civil Rights Data Collection Office for Civil Rights</a>, under the retention tab. All xlsx files relating to grades 1 through 12 were used. To see the preprocessing steps, please look in the repo for more information!
</div>

</main>

<svelte:head>
    <link href="https://fonts.googleapis.com/css2?family=Annapurna+SIL:wght@400;700&family=Kode+Mono:wght@400..700&display=swap" rel="stylesheet">
</svelte:head>

<style>    
  .main {
    border: 1px solid black;
    margin: 1rem;
    padding: 2rem 2rem;
    text-align: center;
  }
  .graph {
    text-align: center;
    display: inline-block;
    margin-left: 0px;
    width:800px;
    height:700px;
  
      }
  
  .usa{
    font-family: "Annapurna SIL", serif;
    font-weight: 700;
    font-size: 40px;
    color: #009996;
  }
  
  .filter {
    display: inline-block;
    margin-left: 0px;
    width:200px;
    height:700px;
      }
  .applets {
    display: inline-block;
    padding: 1rem 1rem;
    vertical-align: middle;
    height:850px;
  }
  .about {
    transform: translateY(100px);
  }
  </style>