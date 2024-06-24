<script setup>
import { ref, onMounted, computed, watch } from "vue";

// the variables are initiated here
const todos = ref([]);
const name = ref("");

// the inputs are declared here
const input_content = ref("");
const input_category = ref(null);

// this sorts the todos
const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return a.createdAt - b.createdAt;
  })
);

// this function creates and adds a todo item
const addTodo = () => {
  // this checks to make sure the content field is not empty or just spaces.
  if (input_content.value.trim() === "" || input_category.value === null) {
    console.log("Please enter a task.");
    return;
  }
  // this grabs the values of all the inputs and creates an object that is then pushed to the todos
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });

  // this clears the values in the input fields after a todo is added
  input_content.value = "";
  input_category.value = null;
};

// this removes the selected todo
const removeTodo = (todo) => {
  // re-assigns todo as itself without that specific task that was requested to be deleted.
  todos.value = todos.value.filter((t) => t !== todo);
};

// watches to see changes to todos and then executes this code
watch(
  todos,
  (newVal) => {
    // this stores in local storage. Seems to work in key value pairs
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  // this tells it to also look inside of todos to see if any element or item(s) in it change.
  { deep: true }
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

// this is kind of like useEffect in react. It runs when the app is loaded or refreeshed (mounted)
onMounted(() => {
  // below lines of code grab the name and todos from local storage. Sets to empty if they dont exist
  name.value = localStorage.getItem("name") || ""; // this grabs name from local storage (browser)
  todos.value = JSON.parse(localStorage.getItem("todos")) || []; // this grabs the tasks
});
</script>

<!-- v-model creates a two way binding between a component and a value -->

<!-- @submit.prevent="addTodo" the '@submit' is shorthand for 'v-on:submit' and it listens for the submit event on a form-->
<!-- the "prevent" is a modifier that tells vue to call "event.preventDefault()" -->
<!-- addTodo is the method or function name to call -->
<!-- in a nutshell the code says on submit prevent default event and call addTodo-->

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Whats up, <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>Create a ToDo</h3>
      <form @submit.prevent="addTodo">
        <h4>Whats on your todo list?</h4>
        <input
          type="text"
          placeholder="e.g. make a video"
          v-model="input_content"
        />
        <h4>Pick a category</h4>

        <div class="options">
          <label
            ><input
              type="radio"
              name="category"
              value="business"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Business</div></label
          >
          <label
            ><input
              type="radio"
              name="category"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div></label
          >
          <!-- {{ input_category }} this line will show the selected option -->
        </div>
        <input type="submit" value="Add todo" />
      </form>
    </section>
    <section class="todo-list">
      <h3>ToDo List</h3>
      <div class="list">
        <div
          v-for="todo in todos_asc"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label
            ><input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span
          ></label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
