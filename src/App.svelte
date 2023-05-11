<script>
  import { createEventDispatcher } from 'svelte';

  let tasks = [];
  let completedTasks = [];
  let newTask = '';

  function addTask() {
    if (newTask) {
      tasks = [...tasks, { id: Date.now(), title: newTask, completed: false }];
      newTask = '';
    }
  }

  function handleKeyPress(event) {
    if (event.key === 'Enter') {
      addTask();
    }
  }

  function toggleTaskCompleted(taskId) {
    tasks = tasks.map(task => {
      if (task.id === taskId) {
        return {
          ...task,
          completed: !task.completed
        };
      }
      return task;
    });

    const completedTask = tasks.find(task => task.id === taskId && task.completed);
    if (completedTask) {
      tasks = tasks.filter(task => task.id !== taskId);
      completedTasks = [...completedTasks, completedTask];
    }
  }

  function removeTask(taskId, isCompleted) {
    if (isCompleted) {
      completedTasks = completedTasks.filter(task => task.id !== taskId);
    } else {
      tasks = tasks.filter(task => task.id !== taskId);
    }
  }

  const dispatch = createEventDispatcher();

  $: dispatch('updateTasks', tasks);
</script>

<main>
  <h1>To-Do App</h1>

  <div class="task-form">
    <input type="text" bind:value="{newTask}" placeholder="Enter a task" on:keydown="{handleKeyPress}" />
    <button on:click="{addTask}">Add Task</button>
  </div>

  <ul>
    {#each tasks as task}
      <li class="{task.completed ? 'completed' : ''}">
        <button on:click="{() => toggleTaskCompleted(task.id)}" class="remove-button">×</button>
        <span>{task.title}</span>
      </li>
    {/each}
  </ul>

  {#if completedTasks.length > 0}
    <div class="completed-tasks">
      <button on:click="{() => completedTasks = []}" class="collapse-button">Completed Tasks</button>
      <ul>
        {#each completedTasks as completedTask}
          <li class="completed-item">
            <button on:click="{() => removeTask(completedTask.id, true)}" class="remove-button">×</button>
            <span>{completedTask.title}</span>
          </li>
        {/each}
      </ul>
    </div>
  {/if}
</main>

<style>
  .task-form {
    margin-bottom: 1em;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    display: flex;
    align-items: center;
    margin-bottom: 0.5em;
  }

  .completed {
    text-decoration: line-through;
    opacity: 0.7;
  }

  .completed-tasks {
    margin-top: 1em;
  }

  .completed-item {
    display: flex;
    align-items: center;
    margin-bottom: 0.5em;
  }

  .remove-button {
    background: none;
    border: none;
    cursor: pointer;
    padding: 0.2em;
    margin-right: 0.5em;
    font-size: 1em;
    color: red;
  }

  .collapse-button {
    background: none;
    border: none;
    cursor: pointer;
    padding: 0.2em;
    font-weight: bold;
    color: rgb(22, 22, 57);
  }
</style>

