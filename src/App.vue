<script setup>
import { onMounted,watch,ref,computed } from 'vue';

const todos=ref([])
const name=ref("")

const input_content=ref("")
const input_category=ref(null)


const todo_asc=computed(()=>todos.value.sort((a,b)=>{
   return b.createdAt - a.createdAt
}))

watch(name,(newVal)=>{
  localStorage.setItem("name",newVal)
})

onMounted(()=>{
  name.value=localStorage.getItem("name") || ""
  todos.value=JSON.parse(localStorage.getItem("todos"))   || []
})

const addTodo=()=>{
  if(input_content.value.trim() ===""  || input_category.value=== null){
    return
  }

  todos.value.push({
    content:input_content.value,
    category: input_category.value,
    done:false,
    createdAt: new Date().getTime()
  })
}

const removeTodo=(todo)=>{
  todos.value=todos.value.filter(t=> t !==todo)
}

watch(todos,newTodos=>{
    localStorage.setItem("todos",JSON.stringify(newTodos))
  },{deep:true})
</script>

<template>
  <main class="app">

    <section class="greeting">
      <h2 class="title">
        What's up <input type="text" placeholder="Name here"  v-model="name">
      </h2>
    </section>

    <section class="create-todo">
      <h2>Create a todo</h2>
         <form @submit.prevent="addTodo">
          <h4>What's on your todo List</h4>
          <input type="text"placeholder="e.g visit family" v-model="input_content">

     

          <h4>Pick a  category</h4>
          <div class="options">
            <label>
              <input type="radio" value="business" name="category" v-model="input_category"/>
              <span class="bubble business"></span>
              <div>business</div>
            </label>

            <label>
              <input type="radio" value="personal" name="category" v-model="input_category"/>
              <span class="bubble personal"></span>
              <div>Personal</div>
            </label>

   
          </div>

          <input type="submit" value="add todo"/>
        </form>
    </section>

    <section class="todo-list">
      <div class="list">
        <div :class="`todo-item ${todo.done && 'done'} `" v-for="todo in todo_asc">
             <label> 
              <input v-model="todo.done" type="checkbox"/>
              <span :class="`bubble ${todo.category}`"></span>
            </label>
            <div class="todo-content">
              <input type="text" v-model="todo.content"/>
            </div>

            <div class="actions">
              <button class="delete" @click="removeTodo(todo)">Delete</button>
            </div>
        </div>
      </div>

    </section>
  </main>
</template>

