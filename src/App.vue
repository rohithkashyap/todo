<template>
  <div class="container" style="margin-bottom: 50px">
    <!-- header -->
    <div class="d-flex justify-content-center">
      <h2>Todo</h2>
      <h6>1.1.0</h6>
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
        <div class="col-6">
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
        <div class="col-6">
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
        <div class="col">
          <input
            class="form-control"
            type="text"
            placeholder="Optional tags"
            v-model="newTags"
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
          v-on:click="isTodoPending = true"
          v-bind:class="{ active: isTodoPending }"
        >
          Pending
        </button>
      </li>
      <li class="nav-item">
        <button
          class="nav-link"
          v-on:click="isTodoPending = false"
          v-bind:class="{ active: !isTodoPending }"
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
    <div v-if="isTodoPending">
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
    <div v-if="!isTodoPending">
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
    if (
      JSON.parse(localStorage.getItem("tagList") != undefined) &&
      JSON.parse(localStorage.getItem("tagList") != null) &&
      JSON.parse(localStorage.getItem("tagColors") != undefined) &&
      JSON.parse(localStorage.getItem("tagColors") != null)
    ) {
      let tagList = JSON.parse(localStorage.getItem("tagList"));
      let tagColors = JSON.parse(localStorage.getItem("tagColors"));
      if (tagList.length == tagColors.length) {
        this.generateTagColorMap(tagList, tagColors);
      }
    }
    if (
      JSON.parse(localStorage.getItem("todoList") != undefined) &&
      JSON.parse(localStorage.getItem("todoList") != null)
    ) {
      console.log("is not nnull");
      this.todoList = JSON.parse(localStorage.getItem("todoList"));
      this.filteredTodoList = this.todoList;
    }
    if (
      JSON.parse(localStorage.getItem("completedTodoList") != undefined) &&
      JSON.parse(localStorage.getItem("completedTodoList") != null)
    ) {
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
      isTodoPending: true,
      newTags: String(""),
      searchText: String(""),
      tagFullColorMap: new Map(),
    };
  },
  methods: {
    addToList() {
      if (this.newTodoItem === "") {
        alert("Invalid entry");
        return;
      }
      let tagArray = this.trimTagsAndSplit(",");
      this.todoList.push({
        id: Date.now(),
        text: this.newTodoItem,
        notes: this.newNotes,
        dueDate: this.dateEnabled ? this.newDueDate : null,
        priority: this.newPriority,
        completed: false,
        completedDate: null,
        tags: tagArray[0],
        tagColors: tagArray[1],
      });
      this.newTodoItem = "";
      this.newNotes = "";
      this.dateEnabled = false;
      this.newDueDate = null;
      this.newPriority = "Medium";
      this.newTags = "";
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
      let tzo = -date.getTimezoneOffset(),
        dif = tzo >= 0 ? "+" : "-",
        pad = function (num) {
          let norm = Math.floor(Math.abs(num));
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
    searchInObject(obj) {
      let text = obj.text.toLowerCase().includes(this.searchText.toLowerCase());
      let notes = obj.notes
        .toLowerCase()
        .includes(this.searchText.toLowerCase());
      let tags = false;
      if (obj.tags != null) {
        tags = obj.tags
          .join(",")
          .toLowerCase()
          .includes(this.searchText.toLowerCase());
      }

      return text || notes || tags;
    },
    trimTagsAndSplit(token) {
      let tagList = [];
      let tagColorMap = [];

      // Remove extra whitespace
      let trimmedTags = this.newTags.trim();
      if (!trimmedTags || trimmedTags == token) {
        return tagList;
      }
      // Check if we only have token
      if (
        trimmedTags.length > 1 &&
        trimmedTags.charAt(trimmedTags.length - 1) == token
      ) {
        trimmedTags = trimmedTags.slice(0, -1);
      }

      // Split based on token
      if (this.newTags.includes(token)) {
        tagList = trimmedTags.split(token);
      } else {
        tagList = [trimmedTags];
      }

      // Loop through tag list to get tag trimmed and assign color
      for (let i = 0; i < tagList.length; i++) {
        let tag = tagList.at(i);

        // Check if map has color, otherwise add
        if (!this.tagFullColorMap.has(tag)) {
          let testColor = this.getRandomColor();
          this.tagFullColorMap.set(tag, testColor);
          console.log("added color");
        }

        // Assign color to tag
        tagColorMap.push(this.tagFullColorMap.get(tag));
      }

      return [tagList, tagColorMap];
    },
    getRandomColor() {
      var letters = "0123456789ABCDEF";
      var color = "#";
      for (var i = 0; i < 6; i++) {
        color += letters[Math.floor(Math.random() * 16)];
      }
      return color;
    },
    generateTagColorMap(keys, values) {
      for (let i = 0; i < keys.length && i < values.length; i++) {
        this.tagFullColorMap.set(keys.at(i), values.at(i));
      }
    },
  },
  watch: {
    todoList: {
      handler() {
        this.filteredTodoList = this.todoList;
        localStorage.todoList = JSON.stringify(this.todoList);
      },
      deep: true,
    },
    completedTodoList: {
      handler() {
        this.filteredCompletedTodoList = this.completedTodoList;
        localStorage.completedTodoList = JSON.stringify(this.completedTodoList);
      },
      deep: true,
    },
    tagFullColorMap: {
      handler() {
        let keys = Array.from(this.tagFullColorMap.keys());
        let values = Array.from(this.tagFullColorMap.values());
        localStorage.setItem("tagList", JSON.stringify(keys));
        localStorage.setItem("tagColors", JSON.stringify(values));
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
      // Otherwise, search for todo text, notes, or tags
      else {
        this.filteredTodoList = this.todoList.filter((obj) =>
          this.searchInObject(obj)
        );

        this.filteredCompletedTodoList = this.completedTodoList.filter((obj) =>
          this.searchInObject(obj)
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
