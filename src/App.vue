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
          <label style="margin-left: 10px" for="dueDateCheckbox"
            >Due date</label
          >
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
          <textarea
            class="form-control"
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
      newDueDate: this.toIsoString(new Date()).slice(0,16),
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
        priority: this.newPriority,
      });
      this.newTodoItem = "";
      this.newNotes = "";
      this.dateEnabled = false;
      this.newDueDate = this.toIsoString(new Date()).slice(0,16);
      this.newPriority = "Medium";
    },
    removeItem(idx) {
      this.todoList = this.todoList.filter(function (obj) {
        return obj.id !== idx;
      });
    },
    toIsoString(date) {
      var tzo = -date.getTimezoneOffset(),
        dif = tzo >= 0 ? "+" : "-",
        pad = function (num) {
          var norm = Math.floor(Math.abs(num));
          return (norm < 10 ? "0" : "") + norm;
        };

      return (
        date.getFullYear() +
        "-" +
        pad(date.getMonth() + 1) +
        "-" +
        pad(date.getDate()) +
        "T" +
        pad(date.getHours()) +
        ":" +
        pad(date.getMinutes()) +
        ":" +
        pad(date.getSeconds()) +
        dif +
        pad(tzo / 60) +
        ":" +
        pad(tzo % 60)
      );
    },
  },
  watch: {
    todoList: {
      handler() {
        localStorage.setItem("todoList", JSON.stringify(this.todoList));
      },
      deep: true,
    },
    dateEnabled: function () {
      this.newDueDate = this.toIsoString(new Date()).slice(0,16);
    },
  },
};
</script>

<style>
.top-buffer {
  margin-top: 10px;
}
</style>
