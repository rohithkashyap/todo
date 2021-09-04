<template>
  <div class="row">
    <div class="col">
      <!-- add a card for todo -->
      <div class="card" :class="style">
        <!-- value == null also checks for == undefined -->
        <div v-if="todo.completedDate != null" class="card-header">
          <small class="text-muted">🏁 Done {{ todo.completedDate }}</small>
        </div>
        <!-- body -->
        <div class="card-body">
          <div class="row">
            <div class="col-1">
              <input
                class="form-check-input"
                type="checkbox"
                v-on:change="completeOrReopenTodo()"
                :checked="todo.completed === true"
              />
            </div>
            <div class="col">
              <p>{{ todo.text }}</p>
            </div>
            <div class="col-2" v-if="todo.notes != '' || todo.dueDate != null ||  (todo.tags != null && todo.tags.length != 0)">
              <button
                class="btn"
                v-if="!expandNotes"
                v-on:click="expandNotes = true"
              >
                🔽
              </button>
              <button
                class="btn"
                v-if="expandNotes"
                v-on:click="expandNotes = false"
              >
                🔼
              </button>
            </div>
            <!-- <div class="col-1">
              <button class="btn btn-outline-dark" v-on:click="deleteTodo()">
                ❌
              </button>
            </div> -->
          </div>
          <p
            id="collapseOne"
            v-if="expandNotes"
            class="card-text collapse show"
            style="white-space: pre-wrap"
          >
            {{ todo.notes }}
          </p>
        </div>

        <!-- footer -->
        <div
          v-show="
            (todo.dueDate != null ||
              (todo.tags != null && todo.tags.length != 0)) &&
            expandNotes
          "
          class="card-footer"
        >
          <div class="row">
            <div class="col-7" v-show="todo.dueDate != null">
              <small class="text-muted">⏲ Due {{ todo.dueDate }}</small>
            </div>
            <div class="col text-end" v-if="todo.tagColors != null">
              <span
                v-for="(tag, index) in todo.tags || []"
                v-bind:key="tag"
                class="badge"
                :style="{ 'background-color': todo.tagColors[index] }"
                style="margin-right: 5px"
              >
                <a
                  href="#"
                  @click.prevent="searchTag(tag)"
                  style="text-decoration: none; text-color: white"
                  >{{ tag }}</a
                ></span
              >
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
  <div style="margin-bottom: 5px"></div>
</template>

<script>
export default {
  name: "TodoItem",
  data() {
    return {
      expandNotes: false,
    };
  },
  props: {
    todo: Object,
  },
  emits: ["delete-todo", "complete-todo", "reopen-todo", "search-tag"],
  methods: {
    deleteTodo() {
      this.$emit("delete-todo", this.todo.id);
    },
    completeOrReopenTodo() {
      if (this.todo.completed === true) {
        this.$emit("reopen-todo", this.todo.id);
      } else {
        this.$emit("complete-todo", this.todo.id);
      }
    },
    searchTag(tag) {
      this.$emit("search-tag", tag);
    },
  },
  computed: {
    style() {
      if (this.todo.completed) {
        return "background-color: rgba(245, 245, 245, 0.4); text-color: rgba(245, 245, 245, 0.4);";
      } else if (this.todo.priority === "High") {
        return "border-danger";
      } else if (this.todo.priority === "Medium") {
        return "border-dark";
      } else {
        return "border-success";
      }
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
a,
a:visited,
a:hover,
a:active {
  color: inherit;
}
input[type="checkbox"] {
  transform: scale(1.5);
}
</style>
