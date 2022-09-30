<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_type = ref('')
const input_deadline = ref(Date.now())

const todos_order_by_deadline = computed(() => {
  return todos.value.sort((a, b) => {
    return a.deadline - b.deadline
  })
})

const createTodo = () => {
  todos.value.push({
    content: input_content.value,
    type: input_type.value,
    deadline: input_deadline.value,
    done: false,
  })
  input_content.value = ''
  input_type.value = ''
  input_deadline.value = Date.now()
}

watch(name, (newName) => {
  localStorage.setItem('name', newName)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
})

</script>

<template>
  <header>
    <h1>Todo List</h1>
    <h2>
      Hello user: <input type="text" placeholder="Name" v-model="name"/>
    </h2>
  </header>
  <main class="main">
    <section class="create-todo">
      <h2>Create Todo</h2>
      <form @submit.prevent="createTodo">
        <input type="text" placeholder="Content" v-model="input_content"/>
        <input type="text" placeholder="Type" v-model="input_type"/>
        <input type="date" v-model="input_deadline"/>
        <button type="submit">Create</button>
      </form>
    </section>
    <section class="todo-list">
      <h2>Todo List</h2>
      <ul>
        <li v-for="todo in todos_order_by_deadline" :key="todo.id">
          <span>{{ todo.content }}</span>
          <span>{{ todo.type }}</span>
          <span>{{ todo.deadline }}</span>
          <button @click="deleteTodo(todo.id)">Delete</button>
        </li>
      </ul>
    </section>
  </main>
</template>
