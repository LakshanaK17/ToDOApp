<script>
  import Task from "./Task.svelte"

  let tasks;
  let isLoading = true;

  fetch("http://localhost:9000/tasks", {
    method: "GET",
    headers: {
      "Content-Type": "application/json",
    },
  })
    .then(async (res) => {
      tasks = await res.json();
      isLoading = false;
    })
    .catch((err) => {
      console.log("error fetching tasks from backend: ", err);
      isLoading = false;
    });

  let input;

  function create() {
    fetch("http://localhost:9000/tasks", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({ name: input.value }),
    }).then(async (res) => {
      const task = await res.json();
      tasks = [...tasks, task];
      input.value = "";
    });
  }

  function handleDelete(e) {
    tasks = tasks.filter((task) => task.id !== e.detail.id);
  }
</script>

<div
  class="mx-auto border border-gray-300 rounded-lg mt-24 max-w-screen-sm py-2.5 px-4"
>
  {#if isLoading}
    <p>Loading...</p>
  {:else if tasks}
    {#each tasks as task}
      <Task {task} on:delete={handleDelete} />
    {/each}

    <form
      class="flex items-stretch relative px-2 py-4"
      on:submit|preventDefault={create}
    >
      <input
        type="text"
        bind:this={input}
        placeholder="Task Name"
        class="border-gray-300 hover:border-gray-500 outline:none focus:ring-2 focus:ring-blue-300 focus:border-blue-500 rounded-l-lg"
      />
      <button
        class="w-11 flex items-center justify-center bg-blue-700 hover:bg-blue-800 text-white outline-none focus:ring-2 focus:ring-blue-300 rounded-r-lg"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          stroke-width="1.5"
          stroke="currentColor"
          class="w-6 h-6"
        >
          <path
            stroke-linecap="round"
            stroke-linejoin="round"
            d="M12 4.5v15m7.5-7.5h-15"
          />
        </svg>
      </button>
    </form>
  {/if}
</div>
