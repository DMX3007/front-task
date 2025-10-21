<template>
  <div class="max-w-lg mx-auto py-20">
    <Card
      v-for="(student, index) in students"
      :index="index"
      :key="student.id"
      :name="student.name"
      v-model="student.age"
      :image-url="student.image"
      class="mb-6"
    />
  </div>
</template>

<script setup lang="ts">
import Card from './components/ExtendedCard.vue'

const students = [
  { id: 1, name: 'Samuel', age: '7', image: 'https://i.pravatar.cc/300' },
  // { id: 2, name: 'Samuel', age: '100', image: 'https://i.pravatar.cc/300' },
  // { id: 3, name: 'Samuel', age: '1000000', image: 'https://i.pravatar.cc/300' }
  ]
</script>
