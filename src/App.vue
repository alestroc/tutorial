<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')




const input_content = ref('')
const input_category = ref(null)

// eslint-disable-next-line vue/no-side-effects-in-computed-properties
const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt

}))

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date()
  })
  input_content.value = ''
  input_category.value = null
}

const removeTodo = todo=> {
  todos.value= todos.value.filter(t=> t!== todo)
}
watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <v-layout class="rounded rounded-md">
  <v-main class="app">
    <v-app-bar title="Application bar">
    </v-app-bar>
    <section class="greeting">
      <h2 class="title">
        Data e ora attuale <input type="text" placeholder="Name here" v-model="name"  />?
      </h2>
    </section>




    <section class="create-todo">

      <h3>CREATE A TODO</h3>

      <form @submit.prevent="addTodo">

        <h4>what's on your todo list?</h4>
        <v-text-field label="e.g. make a video" v-on:submit.prevent  v-model="input_content" ></v-text-field>
        

        <h4>Pick a category</h4>


        <div class="option">

          <v-radio-group v-model="input_category" inline="">
            <v-radio label="Business"  value="business"></v-radio>
            <v-radio label="Personal"  value="personal"></v-radio>
            <v-radio label="Hobby" value="hobby"></v-radio>
          </v-radio-group>
        </div>

         <input class="categoria" type="submit" value="Add todo">
         
      
      </form>
    </section>
    <section>
    <br>

      <h3> TODO LIST</h3>
      <div class="list">

        
        <div v-for="todo in todos_asc" :key="todo.class" :class="`todo-item ${todo.done && 'done'}`"> 

          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>

          <div class="actions">
            <v-btn prepend-icon="$vuetify" class="delete" @click="removeTodo(todo)"> Delete </v-btn>
          </div>

        </div>


      </div>
    </section>
  </v-main>
</v-layout>
</template>
