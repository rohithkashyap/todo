<template>
  <div>
    <form @submit.prevent="addToList()">
      <input type="text" v-model="newTodoItem" /><br /><br />
      <button>Add</button>
    </form>
    <ol>
      <todo-item
        v-for="todo in todoList"
        v-bind:todo="todo"
        v-bind:key="todo.id"
        v-on:remove-item="removeItem"
      ></todo-item>
    </ol>
  </div>
</template>

<script>
import TodoItem from "./components/TodoItem.vue";

export default {
  name: "App",
  components: {
    TodoItem,
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
      this.todoList.push({
        id: this.nextIndex,
        text: this.newTodoItem,
      });
      this.newTodoItem = "";
      console.log(this.nextIndex);
      this.nextIndex++;
    },
    removeItem(idx) {
      this.todoList = this.todoList.filter(function (obj) {
        return obj.id !== idx;
      });
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
