<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const getDate = () => {
  const date = new Date()
  return date.getFullYear() + '-' + (date.getMonth() + 1) + '-' + date.getDate()
}

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_type = ref('')
const input_deadline = ref(getDate())

const todos_order = computed(() => todos.value.sort((a, b) => {
  return a.deadline - b.deadline
}))

const createTodo = () => {
  if (input_content.value.trim() === '' || input_type.value.trim() === '') {
    alert('Empty content or type')
    return
  }
  todos.value.push({
    content: input_content.value,
    type: input_type.value,
    deadline: input_deadline.value
  })
  input_content.value = ''
  input_type.value = ''
  input_deadline.value = getDate()
}

const deleteTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, (newTodos) => {
  localStorage.setItem('todos', JSON.stringify(newTodos))
}, { deep: true }) // deep watch when push new item to array

watch(name, (newName) => {
  localStorage.setItem('name', newName)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
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
        <div class="radio-button">
          <label>
            <input type="radio" value="Work" v-model="input_type"/>
            <div>Work</div>
          </label>
          <label>
            <input type="radio" value="Study" v-model="input_type"/>
            <div>Study</div>
          </label>
          <label>
            <input type="radio" value="Personal" v-model="input_type"/>
            <div>Personal</div>
          </label>
        </div>
        <div class="deadline">
          <label>
            <input type="date" v-model="input_deadline"/>
            <div>Deadline</div>
          </label>
        </div>
        <button type="submit">Create todo</button>
      </form>
    </section>

    <section class="todo-list">
      <h2>Todo List</h2>
      <ul>
        <li v-for="todo in todos_order">
          <span>{{ todo.content }}</span>
          <span>{{ todo.type }}</span>
          <span>{{ todo.deadline }}</span>
          <button @click="deleteTodo(todo)">Delete</button>
        </li>
      </ul>
    </section>
  </main>

</template>
