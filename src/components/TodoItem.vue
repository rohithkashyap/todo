<template>
  <div class="row" style="margin-top: 20px">
    <div class="col">
      <!-- add a card for todo -->
      <div class="card" :class="style">
        <!-- value == null also checks for == undefined -->
        <div v-show="todo.completedDate != null" class="card-header">
          <small class="text-muted">🏁 Done {{ todo.completedDate }}</small>
        </div>
        <div class="card-body">
          <h3 class="card-title">{{ todo.text }}</h3>
          <p class="card-text" style="white-space: pre-wrap">
            {{ todo.notes }}
          </p>
        </div>
        <div
          v-show="todo.dueDate != null || (todo.tags != null && todo.tags.length != 0)"
          class="card-footer"
        >
          <div class="row">
            <div class="col-7" v-show="todo.dueDate != null">
              <small class="text-muted">⏲ Due {{ todo.dueDate }}</small>
            </div>
            <div class="col text-end" v-if="todo.tagColors != null">
              <span v-for="(tag, index) in todo.tags || []" v-bind:key="tag" class="badge" :style="{'background-color': todo.tagColors[index]}" style="margin-right:5px">
                <a href="#" @click.prevent="searchTag(tag)" style="text-decoration: none; text-color:white">{{ tag }}</a></span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- controls for complete or delete -->
  <div class="row">
    <div
      class="col-4"
      v-if="todo.completed === false || todo.completed == null"
    >
      <button
        class="btn btn-outline-success form-control"
        v-on:click="completeTodo()"
      >
        ✔️
      </button>
    </div>
    <div class="col-4" v-if="todo.completed === true">
      <button
        class="btn btn-outline-dark form-control"
        v-on:click="reopenTodo()"
      >
        ↩️
      </button>
    </div>
    <div class="col-4"></div>
    <div class="col-4">
      <button
        class="btn btn-outline-dark form-control"
        v-on:click="deleteTodo()"
      >
        ❌
      </button>
    </div>
  </div>
  <hr />
</template>

<script>
export default {
  name: "TodoItem",
  props: {
    todo: Object,
  },
  emits: ["delete-todo", "complete-todo", "reopen-todo", "search-tag"],
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
    searchTag(tag) {
      this.$emit("search-tag", tag)
    }
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
    tagStyle(obj) {
      console.log(obj)
      return "background-color:" + this.todo.tagColors.get(obj)
    }
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
a, a:visited, a:hover, a:active {
  color: inherit;
}
</style>
