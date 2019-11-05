<template>
  <div
    class="task"
    @click="openTaskModal(task)"
    draggable
    @dragstart="pickUpTask($event, columnIndex, taskIndex)"
    @drop.stop="moveColumnOrTask($event, column.tasks, columnIndex, taskIndex)"
    @dragover.prevent
    @dragenter.prevent
  >
    <span class="w-full font-bold">{{ task.name }}</span>
    <p class="w-full mt-1 text-sm" v-if="task.description">
      {{ task.description }}
    </p>
  </div>
</template>

<script>
export default {
  props: {
    board: {
      type: Object,
      required: true
    },
    taskIndex: {
      type: Number,
      required: true
    },
    columnIndex: {
      type: Number,
      required: true
    },
    task: {
      type: Object,
      required: true
    },
    column: {
      type: Object,
      required: true
    }
  },
  methods: {
    openTaskModal (task) {
      this.$router.push({ name: 'task', params: { id: task.id } })
    },

    pickUpTask (e, fromColumnIndex, fromTaskIndex) {
      e.dataTransfer.effectAllowed = 'move'
      e.dataTransfer.dropEffect = 'move'
      e.dataTransfer.setData('from-column-index', fromColumnIndex)
      e.dataTransfer.setData('from-task-index', fromTaskIndex)
      e.dataTransfer.setData('type', 'task')
    },

    moveColumnOrTask (e, toTasks, toColumnIndex, toTaskIndex) {
      const type = e.dataTransfer.getData('type')
      if (type === 'task') this.moveTask(e, toTasks, toTaskIndex)
      else if (type === 'column') this.moveColumn(e, toColumnIndex)
    },
    moveTask (e, toTasks, toTaskIndex) {
      const fromColumnIndex = e.dataTransfer.getData('from-column-index')
      const fromTasks = this.board.columns[fromColumnIndex].tasks
      const fromTaskIndex = e.dataTransfer.getData('from-task-index')

      this.$store.commit('MOVE_TASK', {
        fromTasks,
        fromTaskIndex,
        toTasks,
        toTaskIndex
      })
    },
    moveColumn (e, toColumnIndex) {
      const fromColumnIndex = e.dataTransfer.getData('from-column-index')

      this.$store.commit('MOVE_COLUMN', {
        fromColumnIndex,
        toColumnIndex
      })
    }
  }
}
</script>

<style>
.task {
  @apply flex items-center flex-wrap shadow mb-2 py-2 px-2 rounded bg-white text-grey-darkest no-underline;
}
</style>
