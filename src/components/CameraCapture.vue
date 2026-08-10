<template>
    <div class="camera-capture">
        <video ref="videoRef" autoplay playsinline class="camera-preview" :class="{ hidden: captured }"></video>

        <img v-if="capturedUrl" :src="capturedUrl" class="camera-result" alt="Foto capturada" />

        <div class="camera-actions">
            <button v-if="!streamActive" type="button" class="camera-btn" @click="startCamera">
                Abrir câmera
            </button>

            <button v-if="streamActive && !captured" type="button" class="camera-btn" @click="capturePhoto">
                Fotografar
            </button>

            <button v-if="captured" type="button" class="camera-btn secondary" @click="retake">
                Refazer
            </button>

            <button v-if="streamActive" type="button" class="camera-btn danger" @click="stopCamera">
                Fechar câmera
            </button>
        </div>

        <p v-if="error" class="camera-error">{{ error }}</p>
    </div>
</template>

<script setup>
import { ref } from 'vue';

const emit = defineEmits(['captured']);

const videoRef = ref(null);
const captured = ref(false);
const capturedUrl = ref(null);
const capturedFile = ref(null);
const streamActive = ref(false);
const error = ref(null);
let stream = null;

async function startCamera() {
    error.value = null;
    try {
        stream = await navigator.mediaDevices.getUserMedia({
            video: {
                facingMode: 'environment',
                width: { ideal: 1280 },
                height: { ideal: 720 },
            },
            audio: false,
        });
        videoRef.value.srcObject = stream;
        streamActive.value = true;
        captured.value = false;
    } catch (err) {
        if (err.name === 'NotAllowedError') {
            error.value = 'Permissão de câmera negada.';
        } else if (err.name === 'NotFoundError') {
            error.value = 'Nenhuma câmera encontrada.';
        } else {
            error.value = 'Erro ao acessar a câmera.';
        }
        console.error(err);
    }
}

function capturePhoto() {
    const video = videoRef.value;
    if (!video) return;

    const canvas = document.createElement('canvas');
    canvas.width = video.videoWidth;
    canvas.height = video.videoHeight;
    const ctx = canvas.getContext('2d');
    ctx.drawImage(video, 0, 0);

    canvas.toBlob(
        (blob) => {
            const file = new File([blob], 'camera-capture.jpg', {
                type: 'image/jpeg',
            });
            capturedUrl.value = URL.createObjectURL(blob);
            capturedFile.value = file;
            captured.value = true;
            emit('captured', file);
        },
        'image/jpeg',
        0.9,
    );
}

function retake() {
    if (capturedUrl.value) URL.revokeObjectURL(capturedUrl.value);
    capturedUrl.value = null;
    capturedFile.value = null;
    captured.value = false;
}

function stopCamera() {
    if (stream) {
        stream.getTracks().forEach((track) => track.stop());
        stream = null;
    }
    streamActive.value = false;
    if (capturedUrl.value) URL.revokeObjectURL(capturedUrl.value);
    capturedUrl.value = null;
    capturedFile.value = null;
    captured.value = false;
}
</script>

<style scoped>
.camera-capture {
    display: flex;
    flex-direction: column;
    gap: 12px;
}

.camera-preview {
    width: 100%;
    max-height: 300px;
    object-fit: contain;
    background: #000000;
    border-radius: 20px;
}

.camera-preview.hidden {
    display: none;
}

.camera-result {
    width: 100%;
    max-height: 300px;
    object-fit: contain;
    border-radius: 20px;
    border: 2px solid #3182ce;
}

.camera-actions {
    display: flex;
    gap: 10px;
    flex-wrap: wrap;
    justify-content: center;
}

.camera-btn {
    padding: 10px 20px;
    border: none;
    border-radius: 20px;
    font-size: 0.875rem;
    font-weight: 600;
    cursor: pointer;
    background-color: #3182ce;
    color: #ffffff;
    transition: background-color 0.2s ease, transform 0.1s ease;
}

.camera-btn:active {
    background-color: #2b6cb0;
}

.camera-btn.secondary {
    background-color: #718096;
}

.camera-btn.secondary:active {
    background-color: #4a5568;
}

.camera-btn.danger {
    background-color: #e53e3e;
}

.camera-btn.danger:active {
    background-color: #c53030;
}

.camera-error {
    color: #c53030;
    background-color: #fff5f5;
    border: 1px solid #fed7d7;
    padding: 10px 16px;
    border-radius: 16px;
    font-size: 0.85rem;
    text-align: center;
}
</style>