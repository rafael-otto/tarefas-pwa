<template>
    <button v-if="showInstallButton" class="install-button" @click="installApp">
        Instalar aplicativo
    </button>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const showInstallButton = ref(false);
let deferredPrompt = null;

onMounted(() => {
    window.addEventListener('beforeinstallprompt', (event) => {
        event.preventDefault();
        deferredPrompt = event;
        showInstallButton.value = true;
    });

    window.addEventListener('appinstalled', () => {
        showInstallButton.value = false;
        deferredPrompt = null;
    });
});

async function installApp() {
    if (!deferredPrompt) return;
    deferredPrompt.prompt();
    const { outcome } = await deferredPrompt.userChoice;
    if (outcome === 'accepted') {
        showInstallButton.value = false;
    }
    deferredPrompt = null;
}
</script>

<style scoped>
.install-button {
    display: block;
    width: 100%;
    padding: 12px 16px;
    margin-top: 16px;
    background-color: #27ae60;
    color: #ffffff;
    border: none;
    border-radius: 20px;
    font-size: 0.95rem;
    font-weight: 600;
    cursor: pointer;
    transition: background-color 0.2s ease;
    text-align: center;
}

.install-button:hover {
    background-color: #219a52;
}
</style>