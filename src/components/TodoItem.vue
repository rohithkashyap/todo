<template>
  <li> {{ todo.text }} <button v-on:click="remove()"> Remove </button> </li>
  
</template>

<script>
export default {
  name: 'TodoItem',
  props: ['todo'],
  methods: {
    remove() {
      this.$emit("remove-item", this.todo.id)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
