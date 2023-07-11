<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')
const inputContent = ref('')
const todosAscending = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt
  })
)

const addTodo = () => {
  if (inputContent.value.trim() === '') {
    return
  }

  todos.value.push({
    content: inputContent.value,
    done: false,
    createdAt: new Date().getTime()
  })

  inputContent.value = ''
}

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, newValue => {
  localStorage.setItem('todos', JSON.stringify(newValue))
}, {deep: true})

// to store item in local storage
watch(name, (newValue) => {
  localStorage.setItem('name', newValue)
})

// to pull items from local storage
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <main class="flex flex-col gap-5 p-10 bg-pink-100 min-h-screen">

    <section>
      <h2 class="font-bold text-xl flex gap-2">
        Hello 
        <input class="bg-pink-100" type="text" placeholder="name here" v-model="name" />
      </h2>
    </section>

    <section>
      <h3 class="uppercase text-center">Create a todo</h3>
      <form class="flex flex-col gap-5 mt-5" @submit.prevent="addTodo">
        <h4 class="text-center">What's on your todo list?</h4>
        <input
          class="p-2 rounded"
          type="text"
          placeholder="e.g. walk my dog"
          v-model="inputContent"
        />
        <input class="bg-blue-500 p-2 rounded text-white" type="submit" value="Add todo">
      </form>
    </section>

    <section>
      <h3 class="uppercase">Todo list</h3>
        <div class="flex justify-between mt-5 bg-white px-5 py-2 rounded" v-for="todo in todosAscending" :class="`todo-item ${todo.done && 'done'}`">
          <div class="flex items-center gap-5">
            <input type="checkbox" v-model="todo.done">
            <input type="text" v-model="todo.content">
          </div>
          <button class="bg-red-500 rounded p-2 text-white" @click="removeTodo(todo)">Delete</button>
        </div>
    </section>

  </main>
</template>
