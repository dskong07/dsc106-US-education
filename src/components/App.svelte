<script>
  import { json } from "d3";
  import { onMount } from 'svelte';
  import Interactive_Map from "./Interactive_Map.svelte";
  
  let dataset = [];
  onMount(async () => {
		const us = await fetch('https://raw.githubusercontent.com/dkong07/dsc106-p3/main/us-states.json')
			.then(d => d.json())

		dataset = us.features
		console.log({ dataset })
	})
</script>

<main>


  <section class="graph">
    <h2 style="margin-top: 15px">USA</h2>
    <Interactive_Map {dataset}/>
  
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