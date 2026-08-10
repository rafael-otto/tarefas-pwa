<template>
    <div class="login-container">
        <form class="login-form" @submit.prevent="handleLogin">
            <h1>Entrar</h1>

            <div v-if="route.query.registered === 'true'" class="success-message">
                Conta criada com sucesso.
                Faça login para continuar.
            </div>

            <div v-if="errorMessage" class="error-message">{{ errorMessage }}</div>

            <div class="field">
                <label for="email">Email</label>
                <input id="email" v-model="email" type="email" placeholder="seu@email.com" required
                    autocomplete="email" />
            </div>

            <div class="field">
                <label for="password">Senha</label>
                <input id="password" v-model="password" type="password" placeholder="••••••••" required
                    autocomplete="current-password" />
            </div>

            <button type="submit" :disabled="loading">
                {{ loading ? 'Entrando...' : 'Entrar' }}
            </button>

            <p class="register-link">Não possui conta?
                <RouterLink to="/register">Criar conta</RouterLink>
            </p>
        </form>
    </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'; 
import { useRouter, useRoute, RouterLink } from 'vue-router'; 
import { useAuthStore } from '/src/stores/auth.js';

const router = useRouter();
const route = useRoute();
const authStore = useAuthStore();

const email = ref('');
const password = ref('');
const loading = ref(false);
const errorMessage = ref('');

onMounted(() => {
    const msg = sessionStorage.getItem('auth_message');

    if (msg) {
        errorMessage.value = msg;
        sessionStorage.removeItem('auth_message');
    }
});

async function handleLogin() {
    loading.value = true;
    errorMessage.value = '';
    try {
        await authStore.login(email.value, password.value);
        router.push('/');
    } catch (err) {
        errorMessage.value =
            err.response?.data?.detail ??
            'Erro ao entrar. Verifique suas credenciais.';
    } finally {
        loading.value = false;
    }
}
</script>

<style scoped>
.login-container {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
    padding: 20px 0;
}

.login-form {
    background-color: #ffffff;
    width: 100%;
    padding: 28px 20px;
    border-radius: 20px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
    border: 1px solid #e2e8f0;
}

.login-form h1 {
    font-size: 1.5rem;
    font-weight: 700;
    color: #3182ce;
    background-color: #ebf8ff;
    padding: 10px 16px;
    border-radius: 12px;
    margin-bottom: 24px;
    text-align: center;
}

.field {
    display: flex;
    flex-direction: column;
    margin-bottom: 20px;
    width: 100%;
}

.field label {
    font-size: 0.875rem;
    font-weight: 600;
    color: #4a5568;
    margin-bottom: 6px;
    padding-left: 4px;
}

.field input {
    width: 100%;
    padding: 12px 16px;
    font-size: 0.95rem;
    border: 1px solid #cbd5e0;
    border-radius: 20px;
    background-color: #ffffff;
    color: #2d3748;
    transition: border-color 0.2s ease, box-shadow 0.2s ease;
}

.field input:focus {
    outline: none;
    border-color: #3182ce;
    box-shadow: 0 0 0 3px rgba(49, 130, 206, 0.15);
}

button[type="submit"] {
    width: 100%;
    padding: 12px;
    font-size: 0.95rem;
    font-weight: 600;
    color: #ffffff;
    background-color: #3182ce;
    border: none;
    border-radius: 20px;
    cursor: pointer;
    transition: background-color 0.2s ease;
    margin-top: 8px;
}

button[type="submit"]:disabled {
    background-color: #a0aec0;
    cursor: not-allowed;
}

.success-message {
    background-color: #f0fff4;
    color: #276749;
    border: 1px solid #c6f6d5;
    padding: 12px 16px;
    border-radius: 16px;
    font-size: 0.875rem;
    margin-bottom: 20px;
    text-align: center;
    width: 100%;
}

.error-message {
    background-color: #fff5f5;
    color: #c53030;
    border: 1px solid #fed7d7;
    padding: 12px 16px;
    border-radius: 16px;
    font-size: 0.875rem;
    margin-bottom: 20px;
    text-align: center;
    width: 100%;
}

.register-link {
    margin-top: 24px;
    text-align: center;
    font-size: 0.875rem;
    color: #718096;
}

.register-link a {
    color: #3182ce;
    text-decoration: none;
    font-weight: 600;
    padding: 4px 8px;
    border-radius: 12px;
    transition: background-color 0.2s ease;
}

.register-link a:active {
    background-color: #ebf8ff;
}
</style>