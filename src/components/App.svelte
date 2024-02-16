<script>
  import ToDo from '../components/ToDo.svelte';
  import Graph from '../components/Graph.svelte';

  let placeholder = "What do you need to do? (category - task)";
  let todo_text = "";

  let todo_category = [
      { id: 0, text: "Study - Learn D3", completed: false, category: "study" },
      { id: 1, text: "Fun - Prepare for camping trip ", completed: false, category: "fun"  },
  ];

  let todo_record = [];
  let todo_len = 0;
  let todo_names = [];

  $: {
      todo_len = todo_category.length;
      
      // add the new todo and aggregate the current todo list
      // store them as an object and append it to the todo_record array
      if (todo_record.length == 0 || todo_len !== todo_record[todo_record.length-1].size) {
          todo_names = todo_category
              .map((todo) => todo.text)
              .reduce((names_as_string, new_todo)=> names_as_string + new_todo + ", ", "");
          todo_record.push(
              {
                  index: todo_record.length+1,
                  size: todo_len,
                  names: todo_names.slice(0,todo_names.length-2)
              }
          )
      }

      // mutating an array doesn' trigger interactivity
      // therefore, we need to assign it again 
      // to manually update the Graph component
      todo_record = todo_record; 
  }

  let next_id = 2;

  function add() {
      todo_category = todo_category.concat({
          id: next_id,
          text: todo_text,
          completed: false,
          category: todo_text.includes("-") ? todo_text.split("-")[0].trim().toLowerCase() : "other"
      });
      next_id = next_id + 1;
      todo_text = "";
  }

  function removeTodo(id) {
  todo_category = todo_category.filter((todo) => todo.id !== id);
}
</script>

<main>

  <section class="todos">
      <h1>todos</h1>
      <form on:submit|preventDefault={add}>
          <input {placeholder} bind:value={todo_text} />
      </form>

      {#each todo_category as todo (todo.id)}
          <ToDo bind:todo={todo} {removeTodo}/>
      {/each}

      <div class="actions" />
  </section>

  <section class="graph">
      <h2 style="margin-top: 15px">todo pie</h2>
      <Graph bind:todo_category={todo_category}/>
  </section>

</main>

<style>
  @import url("https://fonts.googleapis.com/css2?family=Nunito:wght@300;400;700&display=swap");

  :root {
      --color-bg: #ffffff;
      --color-outline: #c2c2c2;
      --color-shadow: hsl(0, 0%, 0%, 0.1);
      --color-text: #3f4252;
      --color-bg-1: hsla(0, 0%, 0%, 0.2);
      --color-shadow-1: hsl(0, 0%, 96%);
  }

  *,
  *::before,
  *::after {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
  }

  main {
      text-align: center;
      font-family: "Nunito", sans-serif;
      font-weight: 300;
      line-height: 2;
      font-size: 24px;
      color: var(--color-text);
      margin-top: 100px;
  }

  label,
  input,
  button {
      font-family: inherit;
      font-weight: inherit;
      line-height: inherit;
      font-size: 24px;
      width: 100%;
  }

  input {
      padding-left: 40px;
      line-height: 72px;
      font-style: italic;
      border: none;
  }

  ::placeholder {
      color: #9e9e9e;
  }

  h1 {
      font-size: 72px;
      font-weight: 300;
      line-height: 2;
  }

  h2 {
      font-size: 30px;
      font-weight: 300;
      line-height: 1.5;
  }

  .todos {
      display: inline-block;
      vertical-align:top;
      width: 500px;
      background-color: var(--color-bg);
      border-radius: 5px;
      border: 1px solid var(--color-outline);
      box-shadow: 0 0 4px var(--color-shadow);
  }

  .graph {
      display: inline-block;
      margin-left: 50px;
  }

  .actions {
      position: relative;
      display: flex;
      align-items: center;
      justify-content: space-between;
  }
  .actions:before {
      content: "";
      height: 40px;
      position: absolute;
      right: 0;
      bottom: 0;
      left: 0;
      box-shadow: 0 1px 1px var(--color-shadow-1), 0 8px 0 -3px var(--color-shadow-1),
          0 9px 1px -3px var(--color-bg-1), 0 16px 0 -6px var(--color-shadow-1),
          0 17px 2px -6px var(--color-bg-1);
      z-index: -1;
  }
</style>
