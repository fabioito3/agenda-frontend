<template>
  <q-page padding>
    <img
      alt="Quasar logo"
      src="~assets/quasar-logo-vertical.svg"
      style="width: 200px; height: 200px"
    />

    <div class="row items-center justify-between q-mb-md">
      <div class="text-h5">
        Minhas tarefas
      </div>

      <q-btn color="negative" label="Sair" @click="logout" />
    </div>

    <q-input v-model="newTask" type="text" label="Nova Tarefa" class="q-mb-sm"/>
    <q-btn label="Adicionar" @click="addTask" class="q-mb-md"/>

    <q-list bordered class="q-mt-md">
      <q-item v-for="task in tasks" :key="task.id">
        <q-item-section side>
          <q-checkbox v-model="task.completed"  @update:model-value="updateTask(task)"/>
          <!-- {{ task.title }} -->
        </q-item-section>

        <q-item-section>
          <div v-if="editingTaskId !== task.id">
            <span :style="{textDecoration: task.completed ? 'line-through' : 'none' }">
              {{ task.title }}
            </span>
          </div>

          <div v-else>
            <q-input v-model="editedTitle" dense />
          </div>
        </q-item-section>

        <q-item-section side>
          <q-btn v-if="editingTaskId !== task.id" 
          icon="edit" 
          flat 
          @click="startEdit(task)" />

          <q-btn
          v-else 
          icon="save" 
          flat 
          @click="saveEdit(task)" />

          <q-btn 
          icon="delete" 
          flat 
          @click="deleteTask(task.id)" />
        </q-item-section>
      </q-item>
    </q-list>
  </q-page>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import api from 'src/services/api'

const router = useRouter()

const tasks = ref([])
const newTask = ref('')
const editingTaskId = ref(null)
const editedTitle = ref('')

const fetchTasks = async () => {
  try{
    const res = await api.get(`/tasks`)
    tasks.value = res.data
  } catch (error) {
    console.log(error)
    router.push('/login')
  }
}

const addTask = async () => {
  if (!newTask.value) return

  await api.post('/tasks', { title: newTask.value})

  newTask.value = ''

  fetchTasks()
}

const updateTask = async (task) => {
  await api.put(`/tasks/${task.id}`, {
    title: task.title,
    completed: task.completed
  })
}

const deleteTask = async (id) => {
  await api.delete(`/tasks/${id}`)

  fetchTasks()
}

const startEdit = (task) => {
  editingTaskId.value = task.id
  editedTitle.value = task.title
}

const saveEdit = async (task) =>{
  await api.put(`/tasks/${task.id}`, {
    title: editedTitle.value,
    completed: task.completed
  })

  editingTaskId.value = null
  editedTitle.value = ''
  fetchTasks()
}

const logout = () => {
  localStorage.removeItem('token')
  localStorage.removeItem('user')
  router.push('/login')
}

onMounted(() => {
  const token = localStorage.getItem('token')

  if (!token) {
    router.push('/login')
    return
  }

  fetchTasks()
})
</script>
