<script setup>
import { computed, ref } from "vue";
import originalTodos from "./data/todos";

const todos = ref(originalTodos);
const title = ref("");

const activeTodos = computed(() =>
  todos.value.filter((todo) => !todo.completed)
);

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
        <div
          v-for="(todo, i) of todos"
          class="todo"
          :class="{ completed: todo.completed }"
        >
          <label class="todo__status-label">
            <input
              type="checkbox"
              class="todo__status"
              v-model="todo.completed"
            />
          </label>

          <!-- show when todo is being edited -->
          <form v-if="false">
            <input
              class="todo__title-field"
              placeholder="Empty todo will be deleted"
            />
          </form>

          <template v-else>
            <span class="todo__title">{{ todo.title }}</span>
            <button class="todo__remove" @click="todos.splice(i, 1)">×</button>
          </template>

          <!-- add `is-active` class when todo being processed -->
          <div class="modal overlay" :class="{ 'is-active': false }">
            <div class="modal-background has-background-white-ter"></div>
            <div class="loader"></div>
          </div>
        </div>
      </section>

      <!-- Hide the footer if there are no todos -->
      <footer class="todoapp__footer" v-if="todos.length > 0">
        <!-- show the number of not caompleted todos -->
        <span class="todo-count">{{ activeTodos.length }} items left</span>

        <!-- Active link should have the 'selected' class -->
        <nav class="filter">
          <a href="#/" class="filter__link selected">All</a>
          <a href="#/active" class="filter__link">Active</a>
          <a href="#/completed" class="filter__link">Completed</a>
        </nav>

        <!-- this button should be disabled if there are no completed todos -->
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
