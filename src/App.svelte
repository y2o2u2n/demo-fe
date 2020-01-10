<script>
  import { onMount } from 'svelte';

  let tasks = [];
  let name = "";

  onMount(async () => {
	const res = await fetch(`http://localhost:8080/api/v1/tasks`, {method: `GET`});
    tasks = await res.json();
  });

  const addTask = async () => {
	const res = await fetch(
		`http://localhost:8080/api/v1/tasks`,
		{
			method: `POST`,
			headers: {
            	'Content-Type': 'application/json',
			},
			body: JSON.stringify({name: name, done: false})
		}
	);

	const newTask = await res.json();
    tasks = [...tasks, newTask];
    name = "";
  };

  const remove = async (task) => {
	const res = await fetch(`http://localhost:8080/api/v1/tasks/${task.id}`, {method: `DELETE`});
	if (res.ok) {
    	tasks = tasks.filter(i => i !== task);
	}
  };

  const toggle = task => {
    task.done = !task.done;
    tasks = tasks;
  };
</script>

<style>
  div,
  h1 {
    color: #333;
    max-width: 300px;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
      Oxygen-Sans, Ubuntu, Cantarell, "Helvetica Neue", sans-serif;
  }
  #name {
    width: 100%;
  }
  form {
    margin-bottom: 0.5em;
  }
  input[type="text"] {
    outline: none;
    margin: 0;
  }
  input[type="text"]:focus {
    border-color: #dc4f21;
    box-shadow: 0 0 2px #dc4f21;
  }
  input[type="checkbox"] {
    margin: 0 10px 0 0;
  }
  li button {
    float: right;
    border: none;
    background: transparent;
    padding: 0;
    margin: 0;
    color: #dc4f21;
    font-size: 18px;
    cursor: pointer;
  }
  li button:hover {
    transform: scale(2);
  }
  li button:focus {
    outline: #dc4f21;
  }
  li:last-child {
    border-bottom: none;
  }
  label {
    display: block;
    text-transform: uppercase;
    font-size: 0.8em;
    color: #777;
  }
  li {
    list-style: none;
    padding: 6px 10px;
    border-bottom: 1px solid #ddd;
  }
  ul {
    padding-left: 0;
  }
  .done span {
    opacity: 0.4;
  }
</style>

<div>
  <h1>Todo List 🗒️</h1>

  <form on:submit|preventDefault={addTask}>
    <label for="name">Add an task</label>
    <input id="name" type="text" bind:value={name} />
  </form>

  <ul>
    {#each tasks as task}
      <li class:done={task.done}>

        <input type="checkbox" bind:checked={task.done} />
        <span>{task.name}</span>
        <button on:click={() => remove(task)}>&times;</button>
      </li>
    {/each}
  </ul>
</div>
