<template>
    <div class="login-container">
        <form class="login-form" @submit.prevent="handleRegister">
            <h1>Criar Conta</h1>

            <div v-if="errorMessage" class="error-message">
                {{ errorMessage }}
            </div>

            <div class="field">
                <label for="email">Email</label>

                <input id="email" v-model="email" type="email" placeholder="seu@email.com" required
                    autocomplete="email" />
            </div>

            <div class="field">
                <label for="password">Senha</label>

                <input id="password" v-model="password" type="password" placeholder="••••••••" required
                    autocomplete="new-password" />
            </div>

            <div class="field">
                <label for="confirmPassword">
                    Confirmar senha
                </label>

                <input id="confirmPassword" v-model="confirmPassword" type="password" placeholder="••••••••" required
                    autocomplete="new-password" />
            </div>

            <button type="submit" :disabled="loading">
                {{ loading ? 'Criando conta...' : 'Criar conta' }}
            </button>

            <p class="register-link">
                Já possui uma conta?

                <RouterLink to="/login">
                    Entrar
                </RouterLink>
            </p>
        </form>
    </div>
</template>

<script setup>
import { ref } from 'vue';
import { RouterLink, useRouter } from 'vue-router';
import authApi from '/src/api/authApi.js';

const router = useRouter();

const email = ref('');
const password = ref('');
const confirmPassword = ref('');

const loading = ref(false);
const errorMessage = ref('');

async function handleRegister() {
    errorMessage.value = '';

    if (password.value !== confirmPassword.value) {
        errorMessage.value = 'As senhas não coincidem.';
        return;
    }

    loading.value = true;

    try {
        await authApi.register(
            email.value,
            password.value
        );

        router.push('/login?registered=true');

    } catch (err) {
        errorMessage.value =
            err.response?.data?.detail ??
            'Erro ao realizar cadastro.';
    } finally {
        loading.value = false;
    }
}
</script>