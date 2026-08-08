<template>
    <form class="task-form" @submit.prevent="handleSubmit">
        <div class="task-row">
            <input v-model="newTask" type="text" placeholder="Nova tarefa..." class="task-input" />
            <button type="submit" class="task-button" :disabled="uploading">
                {{ editingTask ? 'Alterar' : 'Adicionar' }}
            </button>
            <button v-if="editingTask" type="button" class="task-button-cancel" @click="handleCancel">
                Cancelar
            </button>
        </div>

        <div class="image-section">
            <img v-if="previewUrl || editingTask?.img_url" :src="previewUrl || editingTask?.img_url"
                class="image-preview" alt="Imagem da tarefa" />
                <!-- <button v-if="editingTask.img_url && previewUrl === null" type="button" class="task-button-remove-image" @click="handleRemoveImage">Remover imagem</button> -->
            <label class="image-label" :class="{ disabled: uploading }">
                <span v-if="uploading" class="upload-status">Enviando...</span>
                <span v-else>Adicionar imagem</span>
                <input type="file" accept="image/jpeg,image/png" capture="environment" class="image-input"
                    :disabled="uploading" @change="handleImageChange" />
            </label>

            <!-- Alternativa com preview ao vivo -->
            <button type="button" class="task-button-secondary" @click="showCameraCapture = !showCameraCapture">
                {{ showCameraCapture ? 'Fechar câmera' : 'Abrir preview ao vivo' }}
            </button>
            <CameraCapture v-if="showCameraCapture" @captured="handleCameraCapture" />
        </div>
    </form>
</template>

<script setup>
import { ref, watch } from 'vue'
import tasksApi from '../api/tasksApi.js'
import CameraCapture from './CameraCapture.vue'

const props = defineProps({
    editingTask: {
        type: Object,
        default: null,
    },
})

const emit = defineEmits(['add', 'update', 'cancel'])
const newTask = ref('')
const previewUrl = ref(null)
const imgAttachmentKey = ref(null)
const uploading = ref(false)
const removeImage = ref(false)
const showCameraCapture = ref(false)

watch(
    () => props.editingTask,
    (task) => {
        newTask.value = task ? task.title : ''
        if (previewUrl.value) URL.revokeObjectURL(previewUrl.value);
        previewUrl.value = null
        imgAttachmentKey.value = null
        removeImage.value = false
    },
)

async function handleImageChange(event) {
    const file = event.target.files[0]
    if (!file) return
    if (previewUrl.value) URL.revokeObjectURL(previewUrl.value);
    previewUrl.value = URL.createObjectURL(file)
    uploading.value = true
    try {
        const response = await tasksApi.uploadImage(file)
        imgAttachmentKey.value = response.data.attachment_key
    } catch (err) {
        console.error('Erro ao fazer upload da imagem', err)
        previewUrl.value = null
        imgAttachmentKey.value = null
    } finally {
        uploading.value = false
    }
}

function handleRemoveImage() {
    console.log('Remover imagem')

    previewUrl.value = null
    imgAttachmentKey.value = null
    removeImage.value = true
}

function handleSubmit() {
    if (!newTask.value.trim()) return;
    const payload = {
        title: newTask.value.trim(),
        imgAttachmentKey: imgAttachmentKey.value,
        removeImage: removeImage.value,
    };
    if (props.editingTask) {
        emit('update', props.editingTask.id, payload);
    } else {
        emit('add', payload);
    }
    newTask.value = '';
    if (previewUrl.value) {
        URL.revokeObjectURL(previewUrl.value);
    }
    previewUrl.value = null;
    imgAttachmentKey.value = null;
    removeImage.value = false;
}

function handleCancel() {
    newTask.value = ''
    if (previewUrl.value) URL.revokeObjectURL(previewUrl.value);
    previewUrl.value = null
    imgAttachmentKey.value = null
    emit('cancel')
}

function handleCameraCapture(file) {
    previewUrl.value = URL.createObjectURL(file);
    uploading.value = true;
    tasksApi
        .uploadImage(file)
        .then((response) => {
            imgAttachmentKey.value = response.data.attachment_key;
        })
        .catch((err) => {
            console.error(err);
            previewUrl.value = null;
        })
        .finally(() => {
            uploading.value = false;
        });
}

</script>

<style scoped>
.task-form {
    margin-bottom: 24px;
}

.task-row {
    display: flex;
    gap: 6px;
    margin-bottom: 12px;
}

.task-input {
    flex: 1;
    padding: 12px 16px;
    border: 1px solid #cbd5e0;
    border-radius: 20px;
    font-size: 0.95rem;
    outline: none;
    color: #2d3748;
    transition: border-color 0.2s ease, box-shadow 0.2s ease;
}

.task-input:focus {
    border-color: #3182ce;
    box-shadow: 0 0 0 3px rgba(49, 130, 206, 0.15);
}

.task-button {
    padding: 12px 16px;
    background-color: #3182ce;
    color: #ffffff;
    border: none;
    border-radius: 20px;
    font-size: 0.95rem;
    font-weight: 600;
    cursor: pointer;
    transition: background-color 0.2s ease;
}

.task-button:hover:not(:disabled) {
    background-color: #2b6cb0;
}

.task-button:disabled {
    background-color: #a0aec0;
    cursor: not-allowed;
}

.task-button-cancel {
    padding: 12px 18px;
    background-color: transparent;
    color: #718096;
    border: 1px solid #cbd5e0;
    border-radius: 20px;
    font-size: 0.95rem;
    font-weight: 500;
    cursor: pointer;
    transition: all 0.2s ease;
}

.task-button-cancel:hover {
    background-color: #f7fafc;
    border-color: #a0aec0;
}

.image-section {
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    justify-content: center;
    gap: 12px;
    padding: 16px;
    background-color: #f7fafc;
    border-radius: 20px;
    border: 2px dashed #e2e8f0;
}

.image-preview {
    width: 52px;
    height: 52px;
    object-fit: cover;
    border-radius: 12px;
    border: 1px solid #cbd5e0;
    flex-shrink: 0;
}

.image-label {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    gap: 6px;
    padding: 8px 16px;
    background-color: #ffffff;
    border: 1px solid #3182ce;
    color: #3182ce;
    border-radius: 20px;
    font-size: 0.875rem;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.2s ease;
}

.image-label:hover:not(.disabled) {
    background-color: #ebf8ff;
}

.image-label.disabled {
    opacity: 0.6;
    cursor: not-allowed;
}

.image-input {
    display: none;
}

.task-button-secondary {
    padding: 8px 16px;
    background-color: #ffffff;
    color: #4a5568;
    border: 1px solid #cbd5e0;
    border-radius: 20px;
    font-size: 0.875rem;
    font-weight: 500;
    cursor: pointer;
    transition: all 0.2s ease;
}

.task-button-secondary:hover {
    background-color: #edf2f7;
    color: #2d3748;
}

.upload-status {
    color: #718096;
}

.task-button-remove-image {
    padding: 8px 16px;
    background-color: transparent;
    border: 1px solid #feb2b2;
    color: #e53e3e;
    border-radius: 20px;
    cursor: pointer;
    font-size: 0.875rem;
    font-weight: 600;
    transition: all 0.2s ease;
}

.task-button-remove-image:hover {
    background-color: #fff5f5;
    border-color: #fc8181;
}

.image-help {
    font-size: 0.75rem;
    color: #a0aec0;
    margin: 0;
    width: 100%;
    text-align: center;
}

.image-section> :deep(.camera-capture),
.image-section>.camera-capture {
    width: 100%;
    margin-top: 8px;
}
</style>