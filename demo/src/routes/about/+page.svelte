<script>
  import axios from 'axios';
  import { onMount } from 'svelte';

  let todos = [];

  const fetchData = async () => {
    try {
      const response = await axios.get('http://localhost:9090/todos');
      todos = response.data;
    } catch (error) {
      console.error('Error fetching todos:', error);
    }
  };

  onMount(() => {
    fetchData();
  });

  async function addTodo() {
    const item = prompt('Enter a new todo item:');
    if (item) {
      try {
        const response = await axios.post('/todos', {
          item,
          completed: false
        });
        const newTodo = response.data;
        todos = [...todos, newTodo];
      } catch (error) {
        console.error('Error adding todo:', error);
      }
    }
  }

  async function toggleTodoStatus(id) {
    try {
      const response = await axios.patch(`/todos/${id}`);
      const updatedTodo = response.data;
      todos = todos.map(todo =>
        todo.id === updatedTodo.id ? updatedTodo : todo
      );
    } catch (error) {
      console.error('Error toggling todo status:', error);
    }
  }
</script>

<h1 class="text-2xl font-bold mb-4">Todo List</h1>

<table class="table-auto w-full">
  <thead>
    <tr>
      <th class="px-4 py-2">#</th>
      <th class="px-4 py-2">Item</th>
      <th class="px-4 py-2">Status</th>
      <th class="px-4 py-2">Check</th>
    </tr>
  </thead>
  <tbody>
    {#each todos as todo}
    <tr>
      <td class="border px-4 py-2">{todo.id}</td>
      <td class="border px-4 py-2">{todo.item}</td>
      <td class="border px-4 py-2">{todo.completed ? "Completed" : "Not Completed"}</td>
      <td class="border px-4 py-2">
        <input type="checkbox" class="form-checkbox" bind:checked={todo.completed} on:change={() => toggleTodoStatus(todo.id)} />
      </td>
    </tr>
    {/each}
  </tbody>
</table>

<button class="mt-4 bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded" on:click={addTodo}>
  Add Todo
</button>