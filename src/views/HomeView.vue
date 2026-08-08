<template>
    <div>
        <p v-if="store.error" class="error-message">{{ store.error }}</p>

        <TaskForm :editing-task="editingTask" @add="handleAdd" @update="handleUpdate" @cancel="handleCancel" />

        <p v-if="store.loading" class="loading-message">Carregando tarefas...</p>

        <template v-else>
            <section v-if="store.pendingTasks.length > 0">
                <h2 class="section-title">Pendentes ({{ store.pendingTasks.length }})</h2>
                <TaskItem v-for="task in store.pendingTasks" :key="task.id" :task="task" @toggle="handleToggle"
                    @remove="handleRemove" @edit="handleEdit" />
            </section>

            <section v-if="store.completedTasks.length > 0">
                <h2 class="section-title">Concluídas ({{ store.completedTasks.length }})</h2>
                <TaskItem v-for="task in store.completedTasks" :key="task.id" :task="task" @toggle="handleToggle"
                    @remove="handleRemove" @edit="handleEdit" />
            </section>

            <p v-if="store.tasks.length === 0" class="empty-message">
                Nenhuma tarefa cadastrada. Adicione uma acima.
            </p>
        </template>

        <InstallButton />
    </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'
import TaskForm from '../components/TaskForm.vue'
import TaskItem from '../components/TaskItem.vue'
import InstallButton from '../components/InstallButton.vue'
import { useTasksStore } from '../stores/tasks.js'

const store = useTasksStore()
const editingTask = ref(null)

onMounted(() => {
    store.fetchTasks()
})

function handleAdd(payload) {
    store.addTask(payload);
}

function handleUpdate(id, payload) {
    store.updateTask(id, payload);
    editingTask.value = null;
}

function handleCancel() {
    editingTask.value = null
}

function handleEdit(task) {
    editingTask.value = task
}

function handleToggle(id) {
    store.toggleTask(id)
}

function handleRemove(id) {
    if (editingTask.value?.id === id) editingTask.value = null
    store.removeTask(id)
}
</script>

<style scoped>
div {
    max-width: 600px;
    margin: 0 auto;
    padding: 24px 16px;
    color: #2c3e50;
}

.section-title {
    font-size: 0.85rem;
    font-weight: 700;
    text-transform: uppercase;
    letter-spacing: 0.5px;
    color: #3182ce;
    background-color: #ebf8ff;
    padding: 6px 14px;
    border-radius: 20px;
    display: inline-flex;
    align-items: center;
    gap: 8px;
    margin-top: 28px;
    margin-bottom: 16px;
}

.error-message {
    color: #c53030;
    background-color: #fff5f5;
    border: 1px solid #fed7d7;
    border-radius: 16px;
    padding: 12px 16px;
    margin-bottom: 16px;
    font-size: 0.9rem;
    display: flex;
    align-items: center;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.02);
}

.loading-message {
    color: #718096;
    font-size: 0.95rem;
    padding: 16px 0;
    text-align: center;
    font-style: italic;
}

.empty-message {
    text-align: center;
    color: #718096;
    margin-top: 32px;
    padding: 32px 16px;
    background-color: #f7fafc;
    border: 2px dashed #e2e8f0;
    border-radius: 20px;
    font-size: 0.95rem;
}

section {
    margin-bottom: 24px;
}
</style>