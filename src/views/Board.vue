<template>
  <div class="board">
    <div class="flex">
      <div
        class="column"
        v-for="(column, $columnIndex) of board.columns"
        :key="$columnIndex"
      >
        <div class="font-bold mb-2">{{ column.name }}</div>
        <div class="task" v-for="task of column.tasks" :key="task.id">
          <span class="w-full font-bold">{{ task.name }}</span>
          <p class="w-full mt-1 text-sm" v-if="task.description">
            {{ task.description }}
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapState } from 'vuex'

export default {
  computed: mapState(['board'])
}
</script>

<style>
.task {
  @apply flex items-center flex-wrap shadow mb-2 py-2 px-2 rounded bg-white text-grey-darkest no-underline;
}

.column {
  @apply bg-grey-light p-2 mr-4 text-left shadow rounded;
  min-width: 350px;
  max-width: 360px;
}

.board {
  @apply p-4 bg-teal-dark h-full overflow-auto;
}

.task-bg {
  @apply pin absolute;
  background: rgba(0, 0, 0, 0.5);
}
</style>
