<template>
  <div class="row" style="margin-top: 20px">
    <div class="col-10">
      <li class="card" :class="style">
        <div class="card-body">
          <h3 class="card-title">{{ todo.text }}</h3>
          <p class="card-text" style="white-space: pre-wrap;">{{ todo.notes }}</p>
        </div>
        <div v-show="todo.dueDate !== null" class="card-footer">
          <small v-show="todo.dueDate !== null" class="text-muted"
            >Due {{ todo.dueDate }}</small
          >
        </div>
      </li>
    </div>
    <div class="col d-flex align-items-center">
      <button class="btn-close" v-on:click="remove()"></button>
    </div>
  </div>
</template>

<script>
export default {
  name: "TodoItem",
  props: ["todo"],
  methods: {
    remove() {
      this.$emit("remove-item", this.todo.id);
    },
  },
  computed: {
    style() {
      if (this.todo.priority === 'High') {
        return 'border-danger'
      }
      else if (this.todo.priority === 'Medium') {
        return 'border-primary'
      }
      else {
        return 'border-success'
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
