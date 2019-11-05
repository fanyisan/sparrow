<template>
  <div
    class="column"
    draggable
    @dragstart.self="pickUpColumn($event, columnIndex)"
    @drop="
      moveColumnOrTask($event, column.tasks, columnIndex, column.tasks.length)
    "
    @dragover.prevent
    @dragenter.prevent
  >
    <div class="font-bold mb-2 text-xl">{{ column.name }}</div>
    <BoardColumnTask
      class="task"
      v-for="(task, $taskIndex) of column.tasks"
      :key="task.id"
      :columnIndex="columnIndex"
      :taskIndex="$taskIndex"
      :task="task"
      :column="column"
      :board="board"
    />

    <input
      class="w-full bg-transparent p-2"
      placeholder="+ Enter new task"
      @keyup.enter="createTask($event, column.tasks)"
    />
  </div>
</template>

<script>
import BoardColumnTask from '@/components/BoardColumnTask.vue'
export default {
  components: {
    BoardColumnTask
  },
  props: {
    columnIndex: {
      type: Number,
      required: true
    },
    column: {
      type: Object,
      required: true
    },
    board: {
      type: Object,
      required: true
    }
  },
  methods: {
    createTask (e, tasks) {
      this.$store.commit('CREATE_TASK', {
        tasks,
        name: e.target.value
      })
      e.target.value = ''
    },

    pickUpColumn (e, fromColumnIndex) {
      e.dataTransfer.effectAllowed = 'move'
      e.dataTransfer.dropEffect = 'move'
      e.dataTransfer.setData('from-column-index', fromColumnIndex)
      e.dataTransfer.setData('type', 'column')
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
.column {
  @apply bg-grey-light p-2 mr-4 text-left shadow rounded;
  min-width: 350px;
}
</style>
