<template>
  <div
    v-if="sharedState.loading"
    class="fixed top-0 left-0 w-full h-full flex items-center justify-center bg-gray-500 bg-opacity-50"
  >
    <div class="loading-spinner"></div>
  </div>
  <div v-else>
    <ul
      class="w-screen grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-5 gap-4 top-20 absolute p-8"
    >
      <div class="absolute left-1/4 w-1/2">
        <input
          class="shadow-md text-center bg-main-text rounded-lg w-full"
          type="text"
          v-model="search"
          placeholder="Search"
        />
      </div>
      <li
        v-for="product in filteredProducts"
        :key="product.name"
        @click="openProductPage(product.id)"
        class="bg-white rounded-lg shadow-md overflow-hidden curs cursor-pointer transition duration-300 ease-in-out transform hover:scale-105"
      >
        <div>
          <img
            :src="product.image"
            :alt="product.name"
            class="h-64 transform scale-100 object-contain mx-auto p-4"
          />
          <div v-if="sharedState.favorites.includes(product.id)" class="absolute top-2 right-2">
            <font-awesome-icon icon="heart" class="text-red-500 text-3xl" />
          </div>
        </div>
        <div class="flex flex-col relative h-48">
          <div class="p-4 relative">
            <h2 class="text-sm font-semibold text-gray-900">{{ product.name }}</h2>
          </div>
          <div class="p-4 absolute bottom-0">
            <p class="mt-1 text-gray-700">${{ product.price }}</p>
            <div class="flex items-center mt-2">
              <font-awesome-icon icon="star" class="text-yellow-500 text-lg" />
              <span class="ml-1 text-gray-700">{{ product.rating.rate }}</span>
            </div>
          </div>
        </div>
      </li>
    </ul>
  </div>
</template>

<script setup lang="ts">
import { type SharedState } from './App.vue'
import { computed, ref, inject } from 'vue'

const sharedState = inject('sharedState') as SharedState
const details = ref(false)
const search = ref('')

const filteredProducts = computed(() =>
  sharedState.products.data
    ? sharedState.products.data
        .filter((item) => item.name.toLocaleLowerCase().includes(search.value.toLowerCase()))
        .sort((a, b) =>
          sharedState.favorites.includes(a.id) === sharedState.favorites.includes(b.id)
            ? a.price - b.price
            : sharedState.favorites.indexOf(a.id) !== -1
            ? -1
            : 1
        )
    : []
)

function openProductPage(productId: number) {
  details.value = true
  sharedState.productId = productId - 1
  sharedState.list = false
  sharedState.details = details.value
  sharedState.checkout = false
}
</script>

<style>
.loading-spinner {
  border: 16px solid #f3f3f3;
  border-top: 16px solid #3498db;
  border-radius: 50%;
  width: 120px;
  height: 120px;
  animation: spin 0.5s linear infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
</style>
