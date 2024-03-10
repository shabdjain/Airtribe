<template>
  <UContainer
    class="column"
    draggable="true"
    @dragstart.self="pickupColumn($event, columnIndex)"
    @dragenter.prevent
    @dragover.prevent
    @drop.stop="dropItem($event, { toColumnIndex: columnIndex })"
  >
    <div class="column-header mb-4">
      <div>
        {{ column.name }}
        <span class="tasks-count">{{ tasksCount }}</span>
      </div>
      <div>
        <div class="dropdown">
          <UButton class="dot-button" @click="toggleMenu">&#8230;</UButton>
          <ul v-if="menuOpen" class="dropdown-menu">
            <li @click="addNewTask">Add New Task</li>
            <li @click="deleteColumn">Delete Column</li>
          </ul>
        </div>
      </div>
    </div>
    <ul>
      <li v-for="(task, taskIndex) in column.tasks" :key="task.id">
        <UCard
          class="mb-4"
          :class="{ 'open': taskIndex === openTaskIndex }"
          @click="goToTask(task.id)"
          draggable="true"
          @dragstart="
            pickupTask($event, {
              fromColumnIndex: columnIndex,
              fromTaskIndex: taskIndex
            })
          "
          @drop.stop="
            dropItem($event, {
              toColumnIndex: columnIndex,
              toTaskIndex: taskIndex
            })
          "
        >
          <strong>{{ task.name }}</strong>
          <p v-if="taskIndex === openTaskIndex">{{ task.description }}</p>
        </UCard>
      </li>
    </ul>
    <UInput
      v-model="newTaskName"
      type="text"
      placeholder="New"
      icon="i-heroicons-plus-circle-solid"
      @keyup.enter="addTask"
    />
  </UContainer>
</template>

<script setup>
import { useBoardStore } from '../stores/boardStore'
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'

const props = defineProps({
  column: {
    type: Object,
    required: true
  },
  columnIndex: {
    type: Number,
    required: true
  }
})

const boardStore = useBoardStore()
const router = useRouter()

const newTaskName = ref('')
const menuOpen = ref(false)
const editNameState = ref(false)
const openTaskIndex = ref(null)

function addTask() {
  boardStore.addTask({
    taskName: newTaskName.value,
    columnIndex: props.columnIndex
  })
  newTaskName.value = ''
}

function dropItem(event, { toColumnIndex, toTaskIndex }) {
  const type = event.dataTransfer.getData('type')
  const fromColumnIndex = event.dataTransfer.getData('from-column-index')

  if (type === 'task') {
    const fromTaskIndex = event.dataTransfer.getData('from-task-index')

    boardStore.moveTask({
      fromTaskIndex,
      toTaskIndex,
      fromColumnIndex,
      toColumnIndex
    })
  } else if (type === 'column') {
    boardStore.moveColumn({
      fromColumnIndex,
      toColumnIndex
    })
  }
}

function goToTask(taskId) {
  router.push(`/tasks/${taskId}`)
}

function pickupColumn(event, fromColumnIndex) {
  event.dataTransfer.effectAllowed = 'move'
  event.dataTransfer.dropEffect = 'move'
  event.dataTransfer.setData('type', 'column')
  event.dataTransfer.setData('from-column-index', fromColumnIndex)
}

function pickupTask(event, { fromColumnIndex, fromTaskIndex }) {
  event.dataTransfer.effectAllowed = 'move'
  event.dataTransfer.dropEffect = 'move'
  event.dataTransfer.setData('type', 'task')
  event.dataTransfer.setData('from-column-index', fromColumnIndex)
  event.dataTransfer.setData('from-task-index', fromTaskIndex)
}

function toggleMenu() {
  menuOpen.value = !menuOpen.value
}

function toggleEditName() {
  editNameState.value = !editNameState.value
}

function addNewTask() {
  const newTask = {
    id: Math.random().toString(36).substr(2, 9), // Generate unique ID
    name: newTaskName.value,
    description: '', // You can set the description as needed
    showDescription: false, // Initially hide description
    editingDescription: false // Initially not editing description
  }
  props.column.tasks.push(newTask)
  newTaskName.value = ''
}

function deleteColumn() {
  boardStore.deleteColumn(props.columnIndex)
}

function toggleTaskDescription(taskIndex) {
  openTaskIndex.value = openTaskIndex.value === taskIndex ? null : taskIndex
}

const tasksCount = computed(() => {
  return props.column.tasks.length;
});
</script>

<style scoped>
.column {
  background-color: #a1a5aa; /* Change the background color */
  padding: 16px; /* Add padding for spacing */
  border-radius: 8px; /* Add border-radius for rounded corners */
  box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.1); /* Add box-shadow for depth */
}
.tasks-count {
  font-size: smaller;
  margin-left: 8px; /* Adjust as needed */
  color: #888;
}

.dropdown {
  position: relative;
  display: inline-block;
}

.dot-button {
  background: none;
  border: none;
  font-size: 24px;
  cursor: pointer;
  color: black; /* Set color to black */
}

.dropdown-menu {
  position: absolute;
  background-color: #fbf7f7;
  min-width: 160px;
  box-shadow: 0px 8px 16px 0px rgba(0,0,0,0.2);
  z-index: 1;
  list-style-type: none;
  padding: 0;
  margin: 0;
}

.dropdown-menu li {
  padding: 8px 12px;
  cursor: pointer;
}

.dropdown-menu li:hover {
  background-color: #ddd;
}

/* Ensure task descriptions don't overflow */
.UCard {
  max-width: 100%; /* Ensure card doesn't overflow container */
  overflow: hidden; /* Hide overflowing content */
  text-overflow: ellipsis; /* Show ellipsis (...) for overflowed content */
  white-space: nowrap; /* Prevent wrapping */
}

.UCard p {
  margin: 0; /* Reset margins */
  overflow: hidden; /* Hide overflowing content */
  word-wrap: break-word; /* Allow breaking long words */
  display: none; /* Hide description by default */
}

.UCard.open p {
  display: block; /* Display description when task is open */
}

.UCard input {
  width: 100%; /* Ensure input field takes full width */
}
</style>







