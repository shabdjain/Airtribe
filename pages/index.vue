<template>
  <div>
    <AppHeader/>
  <div class="board-wrapper">
    <main class="board">
      <div class="row">
        <div
          v-for="(column, columnIndex) in boardStore.board.columns"
          :key="column.id"
          class="column"
        >
          <BoardColumn :column="column" :columnIndex="columnIndex" />
        </div>
      </div>
      <div class="row">
        <div class="column">
          <UContainer>
            <UInput
              v-model="newColumnName"
              type="text"
              placeholder="Create new column"
              icon="i-heroicons-plus-circle-solid"
              @keyup.enter="addColumn"
              class="create-column-input"
            />
          </UContainer>
        </div>
      </div>
    </main>
    <div v-show="isModalOpen" class="task-bg" @click.self="closeModal">
      <NuxtPage :key="route.fullPath" />
    </div>
  </div>
</div>
</template>

<script setup>
import { useBoardStore } from '../stores/boardStore'
import { useRoute, useRouter } from 'vue-router'
import { ref, computed } from 'vue'

const boardStore = useBoardStore()
const route = useRoute()
const router = useRouter()

const newColumnName = ref('')

const isModalOpen = computed(() => {
  return route.name === 'index-tasks-id'
})

function addColumn() {
  boardStore.addColumn(newColumnName.value)
  newColumnName.value = ''
}

function closeModal() {
  router.push('/')
}
</script>
<script>
import AppHeader from '../components/inc/AppHeader.vue'; // Adjust the path accordingly

export default {
  components: {
    AppHeader
  }
}
</script>
<style scoped>
.board-wrapper {
  display: flex;
  flex-direction: column;
}

.board {
  display: flex;
  flex-wrap: wrap;
}

.column {
  flex: 0 0 calc(33.33% - 16px);
  margin: 8px;
}

.row {
  display: flex;
  flex-wrap: wrap;
}

.create-column-input {
  width: 100%;
}

@media screen and (max-width: 768px) {
  .board {
    flex-direction: column;
  }

  .column {
    flex: 0 0 calc(100% - 16px);
    margin: 8px 0;
  }
}
</style>


