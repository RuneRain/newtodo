<script setup>
import { computed, onMounted, ref, watch } from 'vue';

const todos = ref([]);
const name = ref('');

const input_content = ref('');
const input_category = ref(null);

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null ){ 
    return
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    createAt: new Date().getTime(),
    done:false,
    editable:false
  })
  input_content.value = '';
  input_category.value= null;
  console.log('todos: ',todos)
}

const todos_asc = computed(()=> todos.value.sort((a,b) => { 
  return b.createAt - a.createAt } ))
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t)=> t !== todo );
} 


watch(name, (newVal) => {
  localStorage.setItem('name',newVal)
})
watch(todos, (newVal) => {
  localStorage.setItem('todos',JSON.stringify(newVal))
},{ deep:true } )
onMounted(()=>{
  name.value = localStorage.getItem('name') || '';
  todos.value = JSON.parse(localStorage.getItem('todos') || [])
})

</script>

<template>
  <main class="app">
    <section class="greeting">
      <h1 class="title">
        What'S Up?
        <input type="text" placeholder="name here" v-model="name">
      </h1>
    </section>
    <section class="create-todo">
      <form id="new-todo-form" v-on:submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input type="text" id="content" 
        placeholder="example. vue project set"
        v-model="input_content">
        <div class="options">
          <label>
            <input type="radio" name="category" id="category1" value="today" v-model="input_category">
            <span class="bubble today"></span>
            <div>today</div>
          </label>
          <label>
            <input type="radio" name="category" id="category2" value="study"  v-model="input_category">
            <span class="bubble study"></span>
            <div>study</div>
          </label>
        </div>
        <input type="submit" value="Add TODO">
      </form>
    </section>
    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list" id="todo-list">
        <div v-for="todo in todos_asc" v-bind:class ="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done">
            <span :class="`bubble ${todo.category}`">
            </span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">
              Delete
            </button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
