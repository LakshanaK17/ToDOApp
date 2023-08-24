<script>
  import axios from 'axios';
  import { onMount } from 'svelte';

  let todos = [];

  const fetchData = async () => {
    try {
      const response = await axios.get('/todos');
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

<h1>Todo List</h1>

<table>
  <thead>
    <tr>
      <th>ID</th>
      <th>Item</th>
      <th>Completed</th>
    </tr>
  </thead>
  <tbody>
    {#each todos as todo}
    <tr>
      <td>{todo.id}</td>
      <td>{todo.item}</td>
      <td>{todo.completed ? "Completed" : "Not Completed"}</td>
      <td><input type="checkbox" bind:checked={todo.completed} on:change={() => toggleTodoStatus(todo.id)} /></td>
    </tr>
    {/each}
  </tbody>
</table>

<button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded" on:click={addTodo}>
  Add Todo
</button>
