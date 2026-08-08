<template>
    <div v-if="!isOnline" class="offline-banner">
        Você está offline. Algumas funcionalidades podem estar indisponíveis.
    </div>
</template>

<script setup>
import { useOnlineStatus } from '/src/composables/useOnlineStatus.js';

const { isOnline } = useOnlineStatus();
</script>

<style scoped>
.offline-banner {
    background-color: #e53e3e;
    color: #ffffff;
    text-align: center;
    padding: 10px 16px;
    font-size: 0.85rem;
    font-weight: 600;
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    width: 100%;
    z-index: 1000;
    border-radius: 0 0 16px 16px;
    box-shadow: 0 4px 12px rgba(229, 62, 62, 0.2);
}
</style>