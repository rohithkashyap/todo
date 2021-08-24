<template>
  <div class="row" style="margin-top: 20px">
    <div class="col">
      <!-- add a card for todo -->
      <div class="card" :class="style">
        <!-- value == null also checks for == undefined -->
        <div v-show="todo.completedDate != null" class="card-header">
          <small class="text-muted">Done {{ todo.completedDate }}</small>
        </div>
        <div class="card-body">
          <h3 class="card-title">{{ todo.text }}</h3>
          <p class="card-text" style="white-space: pre-wrap">
            {{ todo.notes }}
          </p>
        </div>
        <div v-show="todo.dueDate != null" class="card-footer">
          <small class="text-muted">Due {{ todo.dueDate }}</small>
        </div>
      </div>
    </div>
  </div>

  <!-- controls for complete or delete -->
  <div class="row">
    <div class="col-4" v-if="todo.completed === false || todo.completed == null">
      <button class="btn btn-outline-dark form-control" v-on:click="completeTodo()">
        Done ✔️
      </button>
    </div>
    <div class="col-4" v-if="todo.completed === true">
      <button class="btn btn-outline-dark form-control" v-on:click="reopenTodo()">
        Re-open ↩️
      </button>
    </div>
    <div class="col-4"></div>
    <div class="col-4">
      <button class="btn btn-outline-dark form-control" v-on:click="deleteTodo()">
        Delete ❌
      </button>
    </div>
  </div>
  <hr>
</template>

<script>
export default {
  name: "TodoItem",
  props: {
    todo: Object,
  },
  emits: ["delete-todo", "complete-todo", "reopen-todo"],
  methods: {
    deleteTodo() {
      this.$emit("delete-todo", this.todo.id);
    },
    completeTodo() {
      this.$emit("complete-todo", this.todo.id);
    },
    reopenTodo() {
      this.$emit("reopen-todo", this.todo.id);
    },
  },
  computed: {
    style() {
      if (this.todo.completed) {
        return "background-color: rgba(245, 245, 245, 0.4); text-color: rgba(245, 245, 245, 0.4);";
      } else if (this.todo.priority === "High") {
        return "border-danger";
      } else if (this.todo.priority === "Medium") {
        return "border-primary";
      } else {
        return "border-success";
      }
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
