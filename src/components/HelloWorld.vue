<script setup>
import { ref } from 'vue'

const longUrl = ref('')
const shortUrl = ref('')
const isLoading = ref(false)
const errorMessage = ref('')

const shortenUrl = async () => {
  if (!longUrl.value) {
    errorMessage.value = 'Masukkan URL terlebih dahulu!'
    return
  }

  isLoading.value = true
  errorMessage.value = ''
  shortUrl.value = ''

  try {
    const response = await fetch('http://localhost:8080/shorten', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ long_url: longUrl.value })
    })
    
    const data = await response.json()
    if (response.ok) {
      shortUrl.value = data.short_url
    } else {
      errorMessage.value = data.error || 'Gagal membuat short URL'
    }
  } catch (error) {
    errorMessage.value = 'Terjadi kesalahan saat menghubungi server'
  }

  isLoading.value = false
}
</script>

<template>
  <div class="container mx-auto p-6 max-w-lg">
    <h1 class="text-2xl font-bold mb-4 text-center">Short URL Generator</h1>
    <input 
      v-model="longUrl" 
      type="text" 
      placeholder="Masukkan URL panjang..." 
      class="w-full p-2 border rounded mb-4"
    />
    <button 
      @click="shortenUrl" 
      :disabled="isLoading" 
      class="w-full bg-blue-500 text-white p-2 rounded disabled:opacity-50"
    >
      {{ isLoading ? 'Memproses...' : 'Perpendek URL' }}
    </button>
    
    <p v-if="errorMessage" class="text-red-500 mt-4">{{ errorMessage }}</p>
    
    <div v-if="shortUrl" class="mt-4 p-4 bg-gray-100 rounded">
      <p>Short URL:</p>
      <a :href="shortUrl" target="_blank" class="text-blue-500">{{ shortUrl }}</a>
    </div>
  </div>
</template>

<style scoped>
.container {
  max-width: 500px;
  margin: auto;
}
</style>