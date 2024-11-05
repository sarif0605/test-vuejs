<template>
  <div class="w-full max-w-lg mx-auto p-5 bg-white shadow-md rounded-md mt-8">
    <h2 class="text-2xl font-bold mb-4">Summary</h2>
    <ul>
      <li v-for="(value, key) in formData" :key="key" class="mb-2">
        <strong>{{ key }}:</strong> {{ value }}
      </li>
    </ul>
    <button
      @click="$emit('goBack')"
      class="mt-4 px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700"
    >
      Kembali untuk Tambah Data
    </button>
  </div>
</template>

<script setup>
const props = defineProps({
  formData: {
    type: Object,
    required: true,
  },
});
</script>
