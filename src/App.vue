<template>
  <div class="container">
    <!-- header -->
    <div class="d-flex justify-content-center">
      <h2>Todo</h2>
    </div>
    <!-- Add a todo -->
    <div class="row">
      <form @submit.prevent="addToList()">
        <div class="row">
          <div class="col">
            <input
              class="form-control"
              type="text"
              placeholder="Add a todo..."
              v-model="newTodoItem"
            />
          </div>
        </div>
        <div class="row" style="margin-top:20px">
          <div class="col text-center">
            <button class="btn btn-primary btn-block" :disabled="newTodoItem === ''">
              Add
            </button>
          </div>
        </div>
      </form>
    </div>

    <hr v-show="todoList.length !== 0" />

    <!-- todo list -->
    <ul style="list-style-type: none">
      <todo-item
        v-for="todo in todoList"
        v-bind:todo="todo"
        v-bind:key="todo.id"
        v-on:remove-item="removeItem"
      ></todo-item>
    </ul>
  </div>
</template>

<script>
import TodoItem from "./components/TodoItem.vue";

export default {
  name: "App",
  components: {
    TodoItem,
  },
  mounted() {
    if (JSON.parse(localStorage.getItem("todoList")) !== null) {
      this.todoList = JSON.parse(localStorage.getItem("todoList"));
      this.nextIndex = this.todoList.length
    }
  },
  data() {
    return {
      todoList: [],
      newTodoItem: "",
      nextIndex: 0,
    };
  },
  methods: {
    addToList() {
      if (this.newTodoItem === "") {
        alert("Invalid entry");
        return;
      }
      this.todoList.push({
        id: this.nextIndex,
        text: this.newTodoItem,
      });
      this.newTodoItem = "";
      this.nextIndex++;
    },
    removeItem(idx) {
      this.todoList = this.todoList.filter(function (obj) {
        return obj.id !== idx;
      });
    },
  },
  watch: {
    todoList: {
      handler() {
        localStorage.setItem("todoList", JSON.stringify(this.todoList));
      },
      deep: true,
    },
  },
};
</script>

<style>
</style>
