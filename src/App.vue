<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const inputContent = ref('')
const inputCategory = ref(null)

const todosAsc = computed(() => todos.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

const addTodo = () => {
	if (inputContent.value.trim() === '' || inputCategory.value === null) {
		return
	}

	todos.value.push({
		content: inputContent.value,
		category: inputCategory.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})

  inputContent.value = "";
  inputCategory.value = null;
}

const removeTodo = (todo) => {
  if(window.confirm(`Supprimer la tâche "${todo.content}" ?`)){
    todos.value = todos.value.filter((t) => t !== todo)
  }
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>


<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Bonjour <input type="text" placeholder="Entrez votre nom" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <form @submit.prevent="addTodo" >
        <h4>Ajouter une nouvelle tâche ?</h4>
        <input type="text" placeholder="Entrer votre tâche" v-model="inputContent" />

        <h4>Sélectionner une catégorie</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" value="business" v-model="inputCategory">
            <span class="bubble business"></span>
            <div>Travail</div>
          </label>
          <label>
            <input type="radio" name="category" value="personal" v-model="inputCategory">
            <span class="bubble personal"></span>
            <div>Personnel</div>
          </label>
        </div>

        <input type="submit" value="Ajouter">
      </form>
    </section>
    <section class="todo-list">
      <h3>Liste des tâches</h3>
      <div class="list">
        <div v-for="todo in todosAsc" :class="`todo-item ${todo.done && 'done'}`">
          <div class="todo-box">
            <label>
              <input type="checkbox" v-model="todo.done" />
              <span :class="`bubble ${
                todo.category == 'business' 
								? 'business' 
								: 'personal'
              }`"></span>
					  </label>
            <div class="todo-content">
              <input type="text" v-model="todo.content" />
            </div>
          </div>
					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Supprimer</button>
					</div>
				</div>
      </div>
    </section>
  </main>
</template>