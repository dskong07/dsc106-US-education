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
  //https://svelte.dev/repl/fd38cae55917408aa43ed9a98425a587?version=3.50.1 
  let yes = true;
  const MAX_SELECTED_ITEMS = 2; // Maximum number of items that can be selected at once.
	const MAX_AVAILABLE_OPTIONS = 10; // How many options should there be in total.
	
	// The list of selected items that we will bind to the input group.
	let selectedItems = [];
	
	// Initiate an array containing the numbers 0 - MAX_AVAILABLE_OPTIONS
	let availableOptions = Array.from(Array(MAX_AVAILABLE_OPTIONS), (_, i) => i+1);
	
	// This is where we will store our selection.
	let chosenItems = [];

	// Update the selected items if the array of selected items is changed.
	const updateSelectedItems = () => {
			// Remove any items from `chosenItems` that are no longer selected.
			chosenItems = chosenItems.filter(x => selectedItems.includes(x))
		
			// Make sure that the new items do not include any already selected items.
			let newItems = selectedItems.filter(x => !chosenItems.includes(x))

			// Merge the previously chosen items, with the newly selected items. 
			chosenItems = [...chosenItems, ...newItems].slice(-MAX_SELECTED_ITEMS);

			// And now update the group of selected items to only include the once that are chose.
			selectedItems = selectedItems.filter(x => chosenItems.includes(x))
	}
	console.log({selectedItems})
  console.log({chosenItems})



</script>


<main>
  <section class="graph">
    <h2 style="margin-top: 15px">USA</h2>
    <Interactive_Map {dataset}{grade2}/>
  
  </section>
</main>

<!-- Grouping component for filtering (hopefully we can move this to the right) -->
<div class = "filter"> 
  <!-- Adding a checkmark for gender, THE default is total which is denoted by clicking both Male and Female -->
  <div class="gender_filter">
    <h2>Filter based on Gender</h2>

    {#each availableOptions as value}
    <label>
      <input
				 type="checkbox"
				 name="items"
				 value={value}	 
				 bind:group={selectedItems}
				 on:change={updateSelectedItems}
      />
      <span>Item {value}</span>
    </label>
    {/each}
  </div>
  
  <!-- A button to apply filter -->.
  <Button class="primary lg">
    Apply Filter
  </Button>
</div>

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

.filter {
  display: inline-block;
  
}
.gender_filter{
  padding-bottom: 25px;
}

label {
		user-select: none;
	}
	label:has(input[type="checkbox"]:checked){
		color:green
	}
</style>