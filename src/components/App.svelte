<script>
  import { json,csv } from "d3";
  import { onMount } from 'svelte';
  import Interactive_Map from "./Interactive_Map.svelte";
  import { read, utils, writeFile } from 'xlsx';
  import Button from "./Button.svelte";
  
  
  let dataset = [];
  onMount(async () => {
		const us = await fetch('https://raw.githubusercontent.com/dkong07/dsc106-p3/main/us-states.json')
			.then(d => d.json())
		dataset = us.features
		console.log({ dataset })
	})

  let grade2 = [];
  onMount(async () => {
    const wb = await json('https://raw.githubusercontent.com/dkong07/dsc106-p3/main/gendery2.json')
    grade2 = wb
    console.log(wb);

  })
  
  //This is for the checkmark, Yes for yes clicking
  let yes = true;

</script>

<!-- Adding a checkmark for gender, THE default is total which is denoted by clicking both Male and Female -->
<div class="gender_filter">
  <h3>Filter based on Gender</h3>
	<label>
    <input type="checkbox" bind:checked={yes} />
    Female
  </label>
  <label>
    <input type="checkbox" bind:checked={yes} />
    Male
  </label>
</div>

<!-- A button to apply filter -->.
<Button class="primary lg">
	Apply Filter
</Button>

<main>
  <section class="graph">
    <h2 style="margin-top: 15px">USA</h2>
    <Interactive_Map {dataset}{grade2}/>
  
  </section>
</main>

<style>    
.main {
  text-align: center;
  font-family: "Nunito", sans-serif;
  font-weight: 300;
  line-height: 2;
  font-size: 24px;
  color: var(--color-text);
  margin-top: 100px;
}
.graph {
  text-align: center;
  display: inline-block;
  margin-left: 0px;
  width:1200px;
  height:700px;
    }
</style>