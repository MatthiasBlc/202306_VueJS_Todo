<script setup>
import { computed, ref } from 'vue';

const todos = ref([])

let newTodo = ''
let filter = ref('all')
let editing = ref(null)
let oldTodo = ref(null)

const remaining = computed(() => todos.value.filter(todo => !todo.completed).length)

const doneTodos = computed(() => todos.value.filter(todo => todo.completed).length)

function addTodo() {
  todos.value.push({
    completed: false,
    name: newTodo
  })
  newTodo = ''
}

const filteredTodos = computed(() => {
  if (filter.value === 'todo') {
    return todos.value.filter(todo => !todo.completed)
  } else if (filter.value === 'done') {
    return todos.value.filter(todo => todo.completed)
  }
  return todos.value
})

const allDone = computed({
  get() {
    return remaining.value === 0
  },
  set(value) {
    todos.value.forEach(todo => {
      todo.completed = value
    });
  }
})

function deleteTodo(todo) {
  todos.value = todos.value.filter(t => t !== todo)
}

function deleteCompleted() {
  todos.value = todos.value.filter(todo => !todo.completed)
}

function editTodo(todo) {
  editing.value = todo
  oldTodo.value = todo.name
}

function doneEdit() {
  editing.value = null
}

function cancelEdit() {
  editing.value.name = oldTodo.value
  doneEdit()
}

//WIP FOCUS WITH SETUP ?? 
const vFocus = {
  mounted: (el, value) => {
    if (value) {
      el.focus()
    }
  }
}
//WIP FOCUS WITH SETUP ?? 


</script>

<template>
  <section class="todoapp">
    <header class="header">

      <h1>Todos</h1>

      <input type="text" class="new-todo" placeholder="Ajouter une tache" v-model="newTodo" @keyup.enter="addTodo">
      <input type="checkbox" class="" v-model="allDone">
    </header>

    <div class="main">

      <ul class="todo-list">
        <li class="todo" v-for="todo in  filteredTodos " :key="todo.id"
          :class="{ completed: todo.completed, editing: todo === editing }">
          <div class="view">
            <input type="checkbox" v-model="todo.completed" class="toggle">
            <label @dblclick="editTodo(todo)">{{ todo.name }}</label>
            <button class="destroy" @click.prevent="deleteTodo(todo)"></button>
          </div>
          <input type="text" class="edit" v-model="todo.name" @keyup.enter="doneEdit" @blur="doneEdit"
            @keyup.escape="cancelEdit" v-focus="todo === editing">
        </li>
      </ul>
    </div>
    <footer class="footer" v-show="todos.length > 0">
      <span class="todo-count">Reste à faire <strong>{{ remaining }}</strong> tâches.</span>
      <ul class="filters">
        <li> <a href="#" :class="{ selected: filter === 'all' }" @click.prevent="filter = 'all'">Toutes</a>
        </li>
        <li> <a href="#" :class="{ selected: filter === 'todo' }" @click.prevent="filter = 'todo'">A faire</a>
        </li>
        <li> <a href="#" :class="{ selected: filter === 'done' }" @click.prevent="filter = 'done'">Faites</a>
        </li>
      </ul>
      <button class="clear-completed" v-show="doneTodos" @click.prevent="deleteCompleted">Supr. finies</button>

    </footer>
  </section>
</template>

<styles src="./ToDos.css"></styles>