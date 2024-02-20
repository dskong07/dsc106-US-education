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
		const us = await fetch('https://raw.githubusercontent.com/dkong07/dsc106-p3/main/us-states.json')
			.then(d => d.json())
		dataset = us.features
		
	})

  let grade2 = [];
  onMount(async () => {
    const wb = await json('https://raw.githubusercontent.com/dkong07/dsc106-p3/main/gendery2.json')
    grade2 = wb
    
  })

  


</script>

<main class="main">

  <div class='applets'>


    <section class="graph">
      <h2 style="margin-top: 15px">USA</h2>
      <InteractiveMap {dataset}{grade2}/>
    
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


</main>

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
}
</style>