<template>
  <div class="container">
    <!-- header -->
    <div class="d-flex justify-content-center">
      <h2>Todo</h2>
    </div>

    <!-- Add a todo -->
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
      <div class="row top-buffer">
        <div class="col-4">
          <input
            class="form-check-input"
            type="checkbox"
            v-model="dateEnabled"
            id="dueDateCheckbox"
          />
          <label style="margin-left:10px" for="dueDateCheckbox">Due date</label>
        </div>
        <div class="col-5">
          <input
            :disabled="!dateEnabled"
            type="datetime-local"
            v-model="newDueDate"
          />
        </div>
      </div>
      <div class="row top-buffer">
        <div class="col-4">
          <label for="">Priority</label>
        </div>
        <div class="col-5">
          <select class="form-select" v-model="newPriority">
            <option>High</option>
            <option selected>Medium</option>
            <option>Low</option>
          </select>
        </div>
      </div>
      <div class="row top-buffer">
        <div class="col">
          <input
            class="form-control"
            type="text"
            placeholder="Optional notes"
            v-model="newNotes"
          />
        </div>
      </div>
      <div class="row top-buffer">
        <div class="col text-center">
          <button
            class="btn btn-primary btn-block"
            :disabled="newTodoItem === ''"
          >
            Add
          </button>
        </div>
      </div>
    </form>

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
      this.nextIndex = this.todoList.length;
    }
  },
  data() {
    return {
      todoList: [],
      newTodoItem: "",
      newNotes: "",
      newDueDate: "",
      newPriority: "Medium",
      dateEnabled: false,
    };
  },
  methods: {
    addToList() {
      if (this.newTodoItem === "") {
        alert("Invalid entry");
        return;
      }
      this.todoList.push({
        id: Date.now(),
        text: this.newTodoItem,
        notes: this.newNotes,
        dueDate: this.dateEnabled ? this.newDueDate : null,
        priority: this.newPriority
      });
      this.newTodoItem = "";
      this.newNotes = "";
      this.priority = "Medium";
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
.top-buffer { margin-top:10px; }

</style>
