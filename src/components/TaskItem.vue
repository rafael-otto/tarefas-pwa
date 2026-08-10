<template>
    <div class="task-item" :class="{ done: task.done }">
        <button v-if="task.img_url" class="image-button" @click="showImage">
            <img v-if="task.img_url" :src="task.img_url" class="task-thumbnail" alt="Imagem da tarefa" />
        </button>

        <dialog ref="imageDialog">
            <img :src="task.img_url" class="task-image" alt="Imagem da tarefa" />
            <button @click="closeImage">Fechar</button>
        </dialog>
        <label class="task-label">
            <input type="checkbox" :checked="task.done" @change="$emit('toggle', task.id)" />
            <span class="task-title">{{ task.title }}</span>
        </label>
        <div class="task-actions">
            <button class="task-edit" @click="$emit('edit', task)">Editar</button>
            <button class="task-remove" @click="$emit('remove', task.id)">Remover</button>
        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue'

defineProps({
    task: {
        type: Object,
        required: true,
    },
})

defineEmits(['toggle', 'remove', 'edit'])

const imageDialog = ref(null)

function showImage() {
    imageDialog.value.showModal()
}

function closeImage() {
    imageDialog.value.close()
}
</script>

<style scoped>
.task-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 12px 16px;
    background-color: #ffffff;
    border: 1px solid #e2e8f0;
    border-radius: 30px;
    margin-bottom: 10px;
    box-shadow: 0 2px 6px rgba(0, 0, 0, 0.03);
    transition: all 0.2s ease;
    gap: 12px;
    width: 100%;
}

.task-thumbnail {
    width: 44px;
    height: 44px;
    object-fit: cover;
    border-radius: 10px;
    border: 1px solid #cbd5e0;
    flex-shrink: 0;
}

.task-item.done {
    opacity: 0.65;
    background-color: #f7fafc;
}

.task-label {
    display: flex;
    align-items: center;
    gap: 12px;
    cursor: pointer;
    flex: 1;
    min-width: 0;
}

.task-label input[type='checkbox'] {
    width: 20px;
    height: 20px;
    accent-color: #3182ce;
    cursor: pointer;
    border-radius: 6px;
}

.task-title {
    font-size: 0.95rem;
    color: #2d3748;
    word-break: break-word;
}

.task-item.done .task-title {
    text-decoration: line-through;
    color: #a0aec0;
}

.task-actions {
    display: flex;
    gap: 4px;
    align-items: center;
    flex-shrink: 0;
}

.task-edit {
    background: transparent;
    border: none;
    color: #3182ce;
    cursor: pointer;
    font-size: 0.85rem;
    font-weight: 600;
    padding: 6px 10px;
    border-radius: 12px;
    transition: background-color 0.2s ease;
}

.task-edit:active {
    background-color: #ebf8ff;
}

.task-remove {
    background: transparent;
    border: none;
    color: #e53e3e;
    cursor: pointer;
    font-size: 0.85rem;
    font-weight: 600;
    padding: 6px 10px;
    border-radius: 12px;
    transition: background-color 0.2s ease;
}

.task-remove:active {
    background-color: #fff5f5;
}

.image-button {
    background: transparent;
    border: none;
    cursor: pointer;
    display: flex;
    align-items: center;
    color: #3182ce;
    padding: 6px;
    border-radius: 12px;
    transition: background-color 0.2s ease;
}

.image-button:active {
    background-color: #ebf8ff;
}

dialog {
    border: none;
    border-radius: 20px;
    padding: 16px;
    background-color: #ffffff;
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
    max-width: calc(100% - 32px);
    width: 100%;
    margin: auto;
}

dialog::backdrop {
    background: rgba(0, 0, 0, 0.5);
    backdrop-filter: blur(2px);
}

.task-image {
    max-width: 100%;
    width: 100%;
    height: auto;
    display: block;
    border-radius: 12px;
    margin-bottom: 12px;
    object-fit: contain;
}
</style>