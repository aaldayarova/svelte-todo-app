<script lang="ts">
  import type { FiltersType, ITodo } from "$root/types/todo";
  import { useStorage } from "$root/stores/useStorage";

  //Example 2 of 5: #COMPONENT
  import AddTodo from "./AddTodo.svelte";
  //Example 3 of 5: #COMPONENT
  import Todo from "./Todo.svelte";
  //Example 4 of 5: #COMPONENT
  import TodosLeft from "./TodosLeft.svelte";
  //Example 5 of 5: #COMPONENT
  import FilterTodos from "./FilterTodos.svelte";

  // state
  let todos = useStorage<ITodo[]>("todos", []);

  let selectedFilter: FiltersType = "all";

  // computed
  $: todosAmount = $todos.length;
  $: incompleteTodos = $todos.filter((todo) => !todo.completed).length;
  $: filteredTodos = filterTodos($todos, selectedFilter);
  $: completedTodos = $todos.filter((todo) => todo.completed).length;

  // methods
  function generateRandomId(): string {
    return Math.random().toString(16).slice(2);
  }

  function addTodo(todo: string): void {
    let newTodo: ITodo = {
      id: generateRandomId(),
      text: todo,
      completed: false,
    };
    $todos = [...$todos, newTodo];
  }

  function toggleCompleted(event: MouseEvent): void {
    let { checked } = event.target as HTMLInputElement;

    $todos = $todos.map((todo) => ({
      ...todo,
      completed: checked,
    }));
  }

  function completeTodo(id: string): void {
    $todos = $todos.map((todo) => {
      if (todo.id === id) {
        todo.completed = !todo.completed;
      }
      return todo;
    });
  }

  function removeTodo(id: string): void {
    $todos = $todos.filter((todo) => todo.id !== id);
  }

  function editTodo(id: string, newTodo: string): void {
    let currentTodo = $todos.findIndex((todo) => todo.id === id);
    $todos[currentTodo].text = newTodo;
  }

  function setFilter(newFilter: FiltersType): void {
    selectedFilter = newFilter;
  }

  function filterTodos(todos: ITodo[], filter: FiltersType): ITodo[] {
    switch (filter) {
      case "all":
        return todos;
      case "active":
        return todos.filter((todo) => !todo.completed);
      case "completed":
        return todos.filter((todo) => todo.completed);
    }
  }

  function clearCompleted(): void {
    $todos = $todos.filter((todo) => todo.completed !== true);
  }
</script>

<main>
  <h1 class="title">todos</h1>

  <section class="todos">
    <AddTodo {addTodo} {toggleCompleted} {todosAmount} />

    <!-- Example 1 of 3: #CONTROLFLOW   -->
    {#if todosAmount}
      <ul class="todo-list">
        {#each filteredTodos as todo (todo.id)}
          <Todo {todo} {completeTodo} {removeTodo} {editTodo} />
        {/each}
      </ul>

      <div class="actions">
        <TodosLeft {incompleteTodos} />
        <FilterTodos {selectedFilter} {setFilter} />
        <!-- <button class="clear-completed">Clear completed</button> -->
      </div>
    {/if}
  </section>
</main>

<!-- Example 1 of 3: #LOCALSTYLE  -->
<style>
  /* Todos */

  .title {
    font-size: var(--font-80);
    font-weight: inherit;
    text-align: center;
    color: var(--color-title);
  }

  .todos {
    --width: 500px;
    --todos-bg: hsl(0 0% 98%);
    --todos-text: hsl(220 20% 14%);

    width: var(--width);
    color: var(--todos-text);
    background-color: var(--todos-bg);
    border-radius: var(--radius-base);
    border: 1px solid var(--color-gray-90);
    box-shadow: 0 0 4px var(--shadow-1);
  }

  .todo-list {
    list-style: none;
  }

  .actions {
    position: relative;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: var(--spacing-8) var(--spacing-16);
    font-size: 0.9rem;
    border-top: 1px solid var(--color-gray-90);
  }

  .actions:before {
    content: "";
    height: 40px;
    position: absolute;
    right: 0;
    bottom: 0;
    left: 0;
    box-shadow: 0 1px 1px hsla(0, 0%, 0%, 0.2), 0 8px 0 -3px hsl(0, 0%, 96%),
      0 9px 1px -3px hsla(0, 0%, 0%, 0.2), 0 16px 0 -6px hsl(0, 0%, 96%),
      0 17px 2px -6px hsla(0, 0%, 0%, 0.2);
    z-index: -1;
  }
</style>
