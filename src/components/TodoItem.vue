<template>
  <div class="row">
    <div class="col-10">
      <li class="card">
        <div class="card-body">
          {{ todo.text }}
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
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
