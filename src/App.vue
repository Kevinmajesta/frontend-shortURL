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
  <div class="flex flex-col items-center justify-center min-h-screen bg-gradient-to-br from-blue-500 to-purple-600 p-6">
    <div class="w-full max-w-lg bg-white shadow-lg rounded-2xl p-6 transform transition duration-300 hover:scale-105">
      <h1 class="text-3xl font-bold text-center mb-6 text-gray-800">Short URL Generator</h1>
      
      <input 
        v-model="longUrl" 
        type="text" 
        placeholder="Masukkan URL panjang..." 
        class="w-full p-3 border rounded-lg focus:outline-none focus:ring-4 focus:ring-blue-300"
      />
      
      <button 
        @click="shortenUrl" 
        :disabled="isLoading" 
        class="w-full bg-blue-600 text-white p-3 rounded-lg mt-4 text-lg font-semibold disabled:opacity-50 hover:bg-blue-700 transition duration-300"
      >
        {{ isLoading ? 'Memproses...' : 'Perpendek URL' }}
      </button>
      
      <p v-if="errorMessage" class="text-red-500 mt-4 text-center font-medium">{{ errorMessage }}</p>
      
      <div v-if="shortUrl" class="mt-6 p-4 bg-gray-100 rounded-lg text-center shadow-inner">
        <p class="font-semibold text-gray-700">Short URL:</p>
        <a :href="shortUrl" target="_blank" class="text-blue-600 font-medium break-all hover:underline">{{ shortUrl }}</a>
      </div>
    </div>
  </div>
</template>

<style>
body {
  font-family: 'Inter', sans-serif;
}
</style>
