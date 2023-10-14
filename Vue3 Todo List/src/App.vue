<script setup>
import { ref, onMounted, computed, watch } from "vue";
const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

const addTodo = () => {
  if(input_content.value.trim() === '' || input_category.value === null){
    return
  }
    
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })

  input_content.value = ''
  input_category.value = null
};

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo) 
}

watch(todos, (newVal) => {
  localStorage.setItem("todos", JSON.stringify(newVal));
}, {deep: true});
watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
});

function setDisable(){
let get = document.getElementById('check');
get.setAttribute('disabled', '');
}
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Hello, <input type="text" placeholder="Your Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>要增加甚麼苦痛</h3>

      <form @submit.prevent="addTodo">
        <h4>這次要增加甚麼痛苦呢?</h4>
        <input 
          type="text" 
          placeholder="睡覺" 
          v-model="input_content" />

          <h4>Pick a category</h4>

          <div class="options">

            <label>
              <input 
                type="radio" 
                name="category"
                value="business"
                v-model="input_category"
                />
                <span class="bubble business"></span>
                <div>工作</div>
            </label>
            <label>
              <input 
                type="radio" 
                name="category"
                value="personal"
                v-model="input_category"
                />
                <span class="bubble personal"></span>
                <div>個人</div>
            </label>
          </div>
          <input type="submit" value="增加痛苦">
      </form>
    </section>

    <section class="todo-list">
      <h3>痛苦簿</h3>
      <div class="list">

        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">

          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${ todo.category }`"></span>
          </label>

          <div class="todo-content">
            <input id="check" type="text" v-model="todo.content" />
          </div>
          
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>


        </div>

      </div>


    </section>
  </main>
</template>
