<script setup>
  import { ref, watch, onMounted, computed } from 'vue'

  const name = ref('')
  const inputContent = ref('')
  const inputCategory = ref(null)
  const todos = ref([])
  
  const addTodo = () => {
    if (inputContent.value.trim() === '' || inputCategory.value === null) {
      return
    }

    console.log(addTodo)

    todos.value.push({
      content: inputContent.value,
      category: inputCategory.value,
      done: false,
      createdAt: new Date().getTime()
    })
  }

  
  // todos.value wird durch slice method kopiert, damit computed richtig funktioniert
  const todosPriority = computed(() => {
    return todos.value.slice().sort((a,b) => {
      return b.createdAt - a.createdAt
    })
  })


  // filter erstellt neue Array mit bestimmte Kondition, return true wenn todoItem != todo 
  const deleteTodoContent = (todo) => {
    todos.value = todos.value.filter(todoItem => todoItem !== todo)
  }


  // Aederung der Wert von Name beobachten
  watch(name, (oldVal, newVal) => {
    if (oldVal !== undefined) {
      console.log('Letzte Username ist: ${oldVal}')
    }

    localStorage.setItem('name', newVal)
  })


  watch(
    todos,
    (newVal) => {
      localStorage.setItem('todos', JSON.stringify(newVal))
    },
    { deep: true }
  )


  // Nach dem Renden von Component ausnutzen
  onMounted(() => {
    name.value = localStorage.getItem('name') || ''
    todos.value = JSON.parse(localStorage.getItem('todos')) || []
  })

</script>

<template>

  <div class="layout">

    <main class="app">

      <section class="greeting">

        <h2 class="titel">
          Guten Tag!
          <input 
            type="text" 
            placeholder="Schreib your Name hier" 
            v-model="name" 
            />
        </h2>

      </section>

      <section class="createToDo">

        <h3>Hey <span class="userName">{{ name }}</span> ! Erstell deine TodoList</h3>

        <!-- @submit = v-on:submit, prevent hilft, Seite neu Laden zu vermeiden -->
        <form @submit.prevent="addTodo">
          <h4>Was ist heute deine Todo List?</h4>

          <input 
            type="text" 
            placeholder="Z.b ich stehe um 7 Uhr auf." 
            v-model="inputContent" 
          />

          <h4>Wahl eine Kategorie aus</h4>

          <div class="options">
            <label>
              <input
                type="radio"
                name="category"
                id="category1"
                value="business"
                v-model="inputCategory"
              />

              <span class="bubble business"></span>

              <div>business</div>
            </label>

            <label>
              <input
                type="radio"
                name="category"
                id="category2"
                value="personal"
                v-model="inputCategory"
              />
              <span class="bubble personal"></span>
              <div>personal</div>
            </label>

          </div>

          <input 
            type="submit" 
            value="Todo hinzufügen" 
            id="submitToDo" 
          />
        </form>

      </section>

      <section class="todoList">

        <h3>Todo List</h3>
        <div class="list">

            <div v-for="todo in todosPriority" v-bind:key="todo.createdAt" v-bind:class="`todo-item ${todo.done && 'done'}`">

              <label>

                <input 
                  type="checkbox"
                  v-model="todo.done"
                  
                />

                <span v-bind:class="`bubble ${todo.category}`"></span>
              </label>

              <div class="todoContent">
                <input 
                  type="text"
                  v-model="todo.content"
                  class="todoContentList"
                >
              </div>

              <div class="actions">
                <button class="deleteTodoContent" @click="deleteTodoContent(todo)">Delete</button>
              </div>
            </div>
        </div>

      </section>

    </main>
  </div>
</template>

<style>
  
  /* google font importieren  */
  @import url('https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,100;0,300;0,400;0,500;0,700;0,900;1,100;1,300;1,400;1,500;1,700;1,900&display=swap');

  .layout {
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 10rem 30rem;
    background-color: blanchedalmond;
  }
  .app {
    background-color: rgb(242, 242, 242);
    padding: 0.5rem 2rem;
    border-radius: 5px;
    height: 100%;
    width: 100%;
  }

  .greeting h2 {
    font-weight: 300;
  }

  .greeting input {
    border-style: none;
    padding: 0.5rem;
    border-radius: 10px;
  }

  .userName {
    color: #36A386;
  }

  .createToDo h3 {
    font-weight: 200;
  }

  .createToDo form h4 {
    font-weight: 150;
  }

  .createToDo form input {
    border-style: none;
    padding: 0.5rem;
    border-radius: 10px;
    width: 100%;
  }

  .options {
    display: flex;
    justify-content: space-around;
    align-items: center;
  }

  .options label {
    background-color: white;
    padding: 1rem;
    border-radius: 5px;
    width: 40%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }

  input[type='text'] {
    outline: none;
  }

  input[type='text']:focus {
    border-color: #28a745 !important;
    box-shadow: 0 4px 8px rgba(40, 152, 167, 0.4);
    background-color: #fff;
    outline: none;
  }

  /* Original Radio und Checkbox einstecken */
  input[type='radio'], input[type='checkbox'] {
    display: none;
  }

  /* Stil fuer Radio gestalten */

  input[type='radio'] + .bubble, input[type='checkbox'] + .bubble {
    display: inline-block;
    width: 16px;
    height: 16px;
    border-radius: 50%;
    border: 2px solid rgba(0, 213, 255, 0.906);
    background-color: white;
    box-shadow: 0 0 0 4px white;
    vertical-align: middle;
  }

  input[type='radio']:checked + .business {
    background-color: rgb(97, 220, 84);
    border: 2px solid rgb(97, 220, 84);
    box-shadow: 0 0 10px 1px rgb(97, 220, 84);
  }


  input[type='radio']:checked + .personal {
    background-color: pink;
    border: 2px solid pink;
    box-shadow: 0 0 10px 1px pink;
  }


  .bubble.business:hover {
    cursor: pointer;
  }

  .bubble.personal:hover {
    cursor: pointer;
  }


  #submitToDo {
    background-color: rgb(101, 175, 206);
    margin-top: 1em;
    color: white;
  }

  #submitToDo:hover {
    cursor: pointer;
    opacity: 0.7;
  }

  .todo-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    background-color: white;
    margin-bottom: 1rem;
    padding: 0.8rem;
    border-radius: 10px;
  }

  .todoContentList{
    outline: none;
    border: none;
  }

  .deleteTodoContent {
    border: none;
    color: white;
    padding: 0.4em;
    background-color: orangered;
    border-radius: 5px;
    box-shadow: -2px -1px 10px 0px orangered;
  } 

  .deleteTodoContent:hover {
    cursor: pointer;
    opacity: 0.7;
  }

  @media screen and (max-width: 375px) {

    .layout, .app {
      padding: 0rem 0rem;
    }

    .app {
      border-radius: none;
    }
  }

</style>