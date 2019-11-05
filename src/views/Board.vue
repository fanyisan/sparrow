<template>
  <div class="board">
    <div class="flex items-start">
      <BoardColumn
        v-for="(column, $columnIndex) of board.columns"
        :key="$columnIndex"
        :board="board"
        :columnIndex="$columnIndex"
        :column="column"
      />

      <div class="column">
        <input
          class="w-full bg-transparent p-2"
          type="text"
          placeholder="+ Create New Column"
          v-model="newColumnName"
          @keyup.enter="createColumn"
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
import BoardColumn from '@/components/BoardColumn.vue'

export default {
  components: { BoardColumn },
  data () {
    return {
      newColumnName: ''
    }
  },
  computed: {
    ...mapState(['board']),

    isTaskOpened () {
      return this.$route.name === 'task'
    }
  },
  methods: {
    closeTaskModal () {
      this.$router.push({ name: 'board' })
    },
    createColumn () {
      this.$store.commit('CREATE_COLUMN', {
        name: this.newColumnName
      })
      this.newColumnName = ''
    }
  }
}
</script>

<style>
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
