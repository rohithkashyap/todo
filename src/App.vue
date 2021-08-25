<template>
  <div class="container" style="margin-bottom: 50px">
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
            class="btn btn-outline-primary form-control"
            :disabled="newTodoItem === ''"
          >
            Add ➕
          </button>
        </div>
      </div>
    </form>

    <hr style="margin-top: 50px" />

    <!-- Tab for pending/completed -->
    <ul class="nav nav-pills nav-fill">
      <li class="nav-item">
        <button
          class="nav-link"
          v-on:click="pendingActive = true"
          v-bind:class="{ active: pendingActive }"
        >
          Pending
        </button>
      </li>
      <li class="nav-item">
        <button
          class="nav-link"
          v-on:click="pendingActive = false"
          v-bind:class="{ active: !pendingActive }"
        >
          Completed
        </button>
      </li>
    </ul>

    <input
      class="form-control top-buffer"
      type="text"
      placeholder="Search..."
      v-model="searchText"
    />

    <!-- todo list -->
    <div v-if="pendingActive">
      <div class="row center-block text-center" style="margin-top: 20px">
        <h3 v-if="filteredTodoList.length === 0">No pending todos</h3>
      </div>
      <todo-item
        v-for="todo in filteredTodoList"
        v-bind:todo="todo"
        v-bind:key="todo.id"
        v-on:delete-todo="deleteTodo"
        v-on:complete-todo="completeTodo"
      ></todo-item>
    </div>

    <!-- completed list -->
    <div v-if="!pendingActive">
      <div class="row center-block text-center" style="margin-top: 20px">
        <h3 v-if="filteredCompletedTodoList.length === 0">
          No completed todos
        </h3>
      </div>
      <todo-item
        v-for="todo in filteredCompletedTodoList"
        v-bind:todo="todo"
        v-bind:key="todo.id"
        v-on:delete-todo="deleteCompletedTodo"
        v-on:reopen-todo="reopenTodo"
      ></todo-item>
    </div>
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
      this.filteredTodoList = this.todoList;
    }
    if (JSON.parse(localStorage.getItem("completedTodoList")) !== null) {
      this.completedTodoList = JSON.parse(
        localStorage.getItem("completedTodoList")
      );
      this.filteredCompletedTodoList = this.completedTodoList;
    }
  },
  data() {
    return {
      todoList: [],
      completedTodoList: [],
      filteredTodoList: [],
      filteredCompletedTodoList: [],
      newTodoItem: String(""),
      newNotes: String(""),
      newDueDate: null,
      newPriority: "Medium",
      dateEnabled: false,
      pendingActive: true,
      searchText: String(""),
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
        completed: false,
        completedDate: null,
      });
      this.newTodoItem = "";
      this.newNotes = "";
      this.dateEnabled = false;
      this.newDueDate = null;
      this.newPriority = "Medium";
    },
    deleteTodo(idx) {
      this.todoList = this.todoList.filter(function (obj) {
        return obj.id !== idx;
      });
    },
    deleteCompletedTodo(idx) {
      this.completedTodoList = this.completedTodoList.filter(function (obj) {
        return obj.id !== idx;
      });
    },
    completeTodo(idx) {
      let data = this.todoList.find((e) => e.id === idx);
      data.completed = true;
      data.completedDate = this.toIsoString(new Date()).slice(0, 16);
      this.completedTodoList.push(data);
      this.deleteTodo(idx);
    },
    reopenTodo(idx) {
      let data = this.completedTodoList.find((e) => e.id === idx);
      data.completed = false;
      data.completedDate = null;
      this.todoList.push(data);
      this.deleteCompletedTodo(idx);
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
        this.filteredTodoList = this.todoList;
        localStorage.setItem("todoList", JSON.stringify(this.todoList));
      },
      deep: true,
    },
    completedTodoList: {
      handler() {
        this.filteredCompletedTodoList = this.completedTodoList;
        localStorage.setItem(
          "completedTodoList",
          JSON.stringify(this.completedTodoList)
        );
      },
      deep: true,
    },
    dateEnabled: function () {
      this.newDueDate = this.toIsoString(new Date()).slice(0, 16);
    },
    searchText: function () {
      // If empty, return full array
      if (!this.searchText) {
        this.filteredTodoList = this.todoList;
        this.filteredCompletedTodoList = this.completedTodoList;
      }
      // Otherwise, search for todo text
      else {
        this.filteredTodoList = this.todoList.filter(
          (obj) =>
            obj.text.toLowerCase().includes(this.searchText.toLowerCase()) ||
            obj.notes.toLowerCase().includes(this.searchText.toLowerCase())
        );

        this.filteredCompletedTodoList = this.completedTodoList.filter(
          (obj) =>
            obj.text.toLowerCase().includes(this.searchText.toLowerCase()) ||
            obj.notes.toLowerCase().includes(this.searchText.toLowerCase())
        );
      }
    },
  },
};
</script>

<style>
.top-buffer {
  margin-top: 10px;
}
</style>
