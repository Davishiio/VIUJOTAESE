<template>
  <Navbar />
  <div class="menu-container">
    <h2>Menú principal</h2>
    <div class="cards">
      <div class="card" @click="goTo('prestamosCliente')">
        <span class="icon">👁️</span>
        <h3>Ver libros prestados</h3>
      </div>
      <div class="card" @click="goTo('prestarlibro')">
        <span class="icon">📚</span>
        <h3>Prestar libros</h3>
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
.menu-container {
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
  border: 2px solid #ddd;
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
