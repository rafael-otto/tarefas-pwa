<template>
    <header class="app-header">
        <h1>Meus gestor de Tarefas!!!!</h1>
        <nav>
            <router-link to="/">Início</router-link>
            <router-link to="/about">Sobre</router-link>
            <div v-if="authStore.isAuthenticated" class="user-info">

                <span v-if="authStore.userEmail" class="user-email">
                    {{ authStore.userEmail }}
                </span>
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