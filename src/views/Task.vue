<template>
  <div class="task-view">
    <input
      class="w-full flex-no-shrink font-bold p-2"
      :value="task.name"
      @change="updateTaskProperty($event, 'name')"
    />
    <textarea
      class="w-full flex-no-shrink h-64 mt-2 p-2"
      placeholder="+ Enter task description"
      :value="task.description"
      @change="updateTaskProperty($event, 'description')"
    ></textarea>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
export default {
  computed: {
    ...mapGetters(['getTask']),
    task () {
      return this.getTask(this.$route.params.id)
    }
  },
  methods: {
    updateTaskProperty (e, key) {
      this.$store.commit('UPDATE_TASK', {
        task: this.task,
        key,
        value: e.target.value
      })
    }
  }
}
</script>

<style>
.task-view {
  @apply relative flex flex-row flex-wrap bg-white pin mx-4 m-32 mx-auto p-4 text-left rounded shadow;
  max-width: 700px;
}
</style>
