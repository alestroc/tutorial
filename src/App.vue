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
};


const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
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

<script>
export default {
  methods: {
  formatDate(dateString) {
    const date = new Date(dateString);
    const day = date.getUTCDate().toString().padStart(2, '0');
    const month = (date.getUTCMonth() + 1).toString().padStart(2, '0'); // Months are 0-based
    const year = date.getUTCFullYear();
    return `${day}/${month}/${year}`;
  }
}


}
</script>




<template>
  <v-app>
    <v-layout class="rounded rounded-md">
      
      <v-main class="app">

        <div>
          <p>Data e ora attuali: {{ currentDateTime }}</p>
          <!-- Resto del tuo template -->
        </div>




        <section class="create-todo">

          <h3>CREATE A TODO</h3>

          <form @submit.prevent="addTodo">

            <h4>what's on your todo list?</h4>
            <v-textarea rows="2" row-height="10" class="todo-input"  v-on:submit.prevent v-model="input_content"></v-textarea>


            <h4>Pick a category</h4>


            <div class="option">

              <v-radio-group v-model="input_category" inline="">
                <v-radio class="business-radio" label="Business" value="business"></v-radio>
                <v-radio class="personal-radio" label="Personal" value="personal"></v-radio>
                <v-radio class="hobby-radio" label="Hobby" value="hobby"></v-radio>
              </v-radio-group>
            </div>

            <input class="categoria" type="submit" value="Add todo">


          </form>
        </section>
        <section>
          <br>

          <h3> TODO LIST</h3>
          

          <div v-for="todo in todos_asc" :key="todo.id" :class="`todo-item ${todo.done && 'done'} && ${todo.category}`">
            {{ todo.category }}
            <label>
              <input type="checkbox" v-model="todo.done" />
              <span :class="`bubble ${todo.category}`"></span>
            </label>


            <v-textarea rows="1" row-height="10" class="todo-item-text" v-model="todo.content"></v-textarea>
            <span class="date">{{ formatDate(todo.createdAt) }}</span>

            <v-btn prepend-icon="$vuetify" class="delete" @click="removeTodo(todo)"> Delete </v-btn>

          </div>

        </section>
      </v-main>
    </v-layout>
  </v-app>
</template>

