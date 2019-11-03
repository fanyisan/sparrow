<template>
  <div class="board">
    <div class="flex">
      <div
        class="column"
        v-for="(column, $columnIndex) of board.columns"
        :key="$columnIndex"
        draggable
        @dragstart.self="pickUpColumn($event, $columnIndex)"
        @drop="moveColumnOrTask($event, column.tasks, $columnIndex, column.tasks.length)"
        @dragover.prevent
        @dragenter.prevent
      >
        <div class="font-bold mb-2 text-xl">{{ column.name }}</div>
        <div
          class="task"
          v-for="(task, $taskIndex) of column.tasks"
          :key="task.id"
          @click="openTaskModal(task)"
          draggable
          @dragstart="pickUpTask($event, $columnIndex, $taskIndex)"
          @drop.stop="
            moveColumnOrTask($event, column.tasks, $columnIndex, $taskIndex)
          "
          @dragover.prevent
          @dragenter.prevent
        >
          <span class="w-full font-bold">{{ task.name }}</span>
          <p class="w-full mt-1 text-sm" v-if="task.description">
            {{ task.description }}
          </p>
        </div>

        <input
          class="w-full bg-transparent p-2"
          placeholder="+ Enter new task"
          @keyup.enter="createTask($event, column.tasks)"
        />
      </div>
    </div>
    <div class="task-bg" v-if="isTaskOpened" @click.self="closeTaskModal">
      <router-view />
    </div>
  </div>
</template>

<script>
import { mapState } from 'vuex'

export default {
  computed: {
    ...mapState(['board']),

    isTaskOpened () {
      return this.$route.name === 'task'
    }
  },
  methods: {
    openTaskModal (task) {
      this.$router.push({ name: 'task', params: { id: task.id } })
    },
    closeTaskModal () {
      this.$router.push({ name: 'board' })
    },
    createTask (e, tasks) {
      this.$store.commit('CREATE_TASK', {
        tasks,
        name: e.target.value
      })
      e.target.value = ''
    },
    pickUpTask (e, fromColumnIndex, fromTaskIndex) {
      e.dataTransfer.effectAllowed = 'move'
      e.dataTransfer.dropEffect = 'move'
      e.dataTransfer.setData('from-column-index', fromColumnIndex)
      e.dataTransfer.setData('from-task-index', fromTaskIndex)
      e.dataTransfer.setData('type', 'task')
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
.task {
  @apply flex items-center flex-wrap shadow mb-2 py-2 px-2 rounded bg-white text-grey-darkest no-underline;
}

.column {
  @apply bg-grey-light p-2 mr-4 text-left shadow rounded;
  min-width: 350px;
}

.board {
  @apply p-4 bg-teal-dark h-full overflow-auto;
}

.task-bg {
  @apply pin absolute;
  background: rgba(0, 0, 0, 0.5);
}
</style>
