<template>
  <Navbar />
  <div class="menu-gerente-container">
    <h2>Menú del Gerente</h2>
    <div class="cards">
      <div class="card" @click="goTo('prestamosGerente')">
        <span class="icon">🔄</span>
        <h3>Estado de Devoluciones</h3>
      </div>
      <div class="card" @click="goTo('EnviarRecordatorio')">
        <span class="icon">📧</span>
        <h3>Enviar recordatorio</h3>
      </div>
    </div>
  </div>
</template>

<script setup>
import Navbar from '@/components/Navbar.vue'
import { useRouter } from 'vue-router'

const router = useRouter()

const goTo = (ruta) => {
  router.push(`/${ruta}`)
}
</script>

<style scoped>
.menu-gerente-container {
  max-width: 800px;
  margin: 2rem auto;
  text-align: center;
}

.cards {
  display: flex;
  justify-content: center;
  gap: 2rem;
  margin-top: 2rem;
  flex-wrap: wrap;
}

.card {
  width: 250px;
  height: 200px;
  border: 2px solid #ccc;
  border-radius: 12px;
  cursor: pointer;
  transition: 0.3s ease;
  padding: 1rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.card:hover {
  background-color: #f0f0f0;
  transform: scale(1.05);
}

.icon {
  font-size: 3rem;
  margin-bottom: 1rem;
}
</style>
