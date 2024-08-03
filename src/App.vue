<script setup>
import StatusFilter from "./components/StatusFilter.vue";
import TodoItem from "./components/TodoItem.vue";
import { computed, onBeforeMount, ref, watch } from "vue";

const title = ref("");
const status = ref("all");
const todos = ref();

onBeforeMount(() => {
  try {
    const value = localStorage.getItem("todos");
    todos.value = value ? JSON.parse(value) : [];
  } catch (error) {
    todos.value = [];
  }
});

watch(
  todos,
  (newTodos) => {
    localStorage.setItem("todos", JSON.stringify(newTodos));
  },
  { deep: true }
);

const activeTodos = computed(() =>
  todos.value.filter((todo) => !todo.completed)
);

const visibleTodos = computed(() => {
  if (status.value === "active") {
    return activeTodos.value;
  }

  if (status.value === "completed") {
    return todos.value.filter((todo) => todo.completed);
  }

  return todos.value;
});

function addTodo() {
  if (!title.value) return;

  todos.value.push({
    id: Date.now(),
    title: title.value,
    completed: false,
  });

  title.value = "";
}
</script>

<template>
  <div class="todoapp">
    <h1 class="todoapp__title">todos {{ todos.length }}</h1>

    <div class="todoapp__content">
      <header class="todoapp__header">
        <!-- this button should have `active` class only if all todos are completed -->
        <button
          v-if="todos.length > 0"
          class="todoapp__toggle-all"
          :class="{ active: activeTodos.length === 0 }"
        ></button>

        <form @submit.prevent="addTodo">
          <input
            class="todoapp__new-todo"
            placeholder="What needs to be done?"
            v-model="title"
          />
        </form>
      </header>

      <section class="todoapp__main" v-if="todos.length > 0">
        <TodoItem
          v-for="todo in visibleTodos"
          :key="todo.id"
          :todo="todo"
          @delete="todos = todos.filter(({ id }) => todo.id !== id)"
          @update="(updatedTodo) => {
            const index = todos.findIndex(({ id }) => todo.id === id);
            todos[index] = updatedTodo;
          }"
        />
      </section>

      <footer class="todoapp__footer" v-if="todos.length > 0">
        <span class="todo-count">{{ activeTodos.length }} items left</span>

        <StatusFilter v-model="status" />

        <button
          class="todoapp__clear-completed"
          :disabled="todos.length === activeTodos.length"
        >
          Clear completed
        </button>
      </footer>
    </div>

    <!-- DON'T use conditional rendering to hide the notification -->
    <!-- Add the 'hidden' class to hide the message smoothly -->
    <div class="notification is-danger is-light has-text-weight-normal hidden">
      <button class="delete"></button>
      <!-- show only one message at a time -->
      Unable to load todos<br />
      Title should not be empty<br />
      Unable to add a todo<br />
      Unable to delete a todo<br />
      Unable to update a todo<br />
    </div>
  </div>
</template>
