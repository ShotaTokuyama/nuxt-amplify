<script setup lang="ts">
import type { Schema } from "../../amplify/data/resource";
import { generateClient } from "aws-amplify/data";

const client = generateClient<Schema>();

// create a reactive reference to the array of todos
const todos = ref<Array<Schema["Todo"]["type"]>>([]);

function fetchTodos() {
  client.models.Todo.observeQuery().subscribe({
    next: ({ items, isSynced }) => {
      // todos.value = items
      todos.value = [...items];
    },
  });
}

async function createTodo() {
  await client.models.Todo.create({
    content: window.prompt("Todo content?"),
    isDone: false,
  });
  // no more manual refetchTodos required!
  // - fetchTodos()
}

onMounted(() => {
  fetchTodos();
});
</script>

<template>
  <div>
    <button @click="createTodo">Add new todo</button>
    <ul>
      <li v-for="todo in todos" :key="todo.id">
        {{ todo.content }}
      </li>
    </ul>
  </div>
</template>
