<template>
    <header class="app-header">
        <h1>Meus gestor de Tarefas</h1>
        <span v-if="authStore.userEmail" class="user-email">
            {{ authStore.userEmail }}
        </span>
        <nav>
            <router-link to="/">Início</router-link>
            <router-link to="/about">Sobre</router-link>
            <div v-if="authStore.isAuthenticated" class="user-info">
                <button class="logout-btn" @click="handleLogout">
                    Sair
                </button>

            </div>
        </nav>
    </header>
</template>

<script setup>
import { useRouter } from 'vue-router';
import { useAuthStore } from '/src/stores/auth.js';

const router = useRouter();
const authStore = useAuthStore();

function handleLogout() {
    authStore.logout();
    router.push('/login');
}
</script>

<style scoped>
.app-header {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 20px 24px;
    background-color: #ffffff;
    border: 1px solid #e2e8f0;
    border-radius: 0 0 20px 20px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.03);
    gap: 12px;
    width: 100%;
    margin-bottom: 16px;
}

.app-header h1 {
    font-size: 1.5rem;
    font-weight: 700;
    color: #3182ce;
    margin: 0;
    text-align: center;
}

.user-email {
    font-size: 0.875rem;
    color: #718096;
    text-align: center;
    margin-top: -4px;
    background-color: #f7fafc;
    padding: 4px 12px;
    border-radius: 12px;
}

.app-header nav {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 16px;
    width: 100%;
    margin-top: 4px;
}

.app-header nav a {
    color: #4a5568;
    text-decoration: none;
    font-weight: 500;
    font-size: 0.95rem;
    padding: 6px 16px;
    border-radius: 20px;
    transition: all 0.2s ease;
}

.app-header nav a:active {
    color: #3182ce;
    background-color: #ebf8ff;
}

.app-header nav a,
.router-link-active {
    color: #3182ce;
    background-color: #ebf8ff;
    font-weight: 600;
}

.logout-btn {
    background-color: transparent;
    color: #e53e3e;
    border: 1px solid #feb2b2;
    padding: 6px 16px;
    font-size: 0.875rem;
    font-weight: 600;
    border-radius: 20px;
    cursor: pointer;
    transition: all 0.2s ease;
}

.logout-btn:active {
    background-color: #fff5f5;
    border-color: #fc8181;
}
</style>