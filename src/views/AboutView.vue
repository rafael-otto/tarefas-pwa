<template>
    <div class="about">
        <h2>Sobre o aplicativo</h2>
        <p>
            Este é um gerenciador de tarefas desenvolvido como Progressive Web App
            (PWA) utilizando Vue.js 3 e Vite.
        </p>
        <p>
            O aplicativo funciona offline, pode ser instalado no seu dispositivo e
            carrega rapidamente mesmo em conexões lentas.
        </p>

        <h3>Tecnologias utilizadas</h3>
        <ul>
            <li>Vue.js 3 com Composition API</li>
            <li>Vite como ferramenta de build</li>
            <li>Vue Router para navegação</li>
            <li>vite-plugin-pwa para suporte a PWA</li>
            <li>Workbox para gerenciamento de cache</li>
        </ul>
    </div>
</template>

<style scoped>
.about {
    max-width: 600px;
    margin: 0 auto;
    padding: 24px 16px;
    line-height: 1.6;
    color: #2c3e50;
}

.about h2 {
    font-size: 1.4rem;
    font-weight: 700;
    color: #3182ce;
    background-color: #ebf8ff;
    padding: 10px 16px;
    border-radius: 12px;
    margin-bottom: 20px;
    text-align: center;
}

.about h3 {
    font-size: 1.1rem;
    font-weight: 600;
    color: #4a5568;
    margin-top: 28px;
    margin-bottom: 12px;
}

.about p {
    margin-bottom: 16px;
    color: #4a5568;
    font-size: 0.95rem;
}

.about ul {
    padding-left: 0;
    list-style: none;
}

.about li {
    position: relative;
    padding-left: 24px;
    margin-bottom: 10px;
    color: #4a5568;
    font-size: 0.95rem;
}

.about li::before {
    content: "•";
    position: absolute;
    left: 8px;
    color: #3182ce;
    font-weight: bold;
    font-size: 1.1rem;
}
</style>