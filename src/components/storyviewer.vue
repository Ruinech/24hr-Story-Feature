<script setup>
  import { ref, onMounted } from 'vue';
  
  const stories = ref(JSON.parse(localStorage.getItem('stories')) || []);
  const activeStory = ref(null);
  const progress = ref(0);
  let interval = null;
  
  const uploadImage = () => {
    const input = document.createElement('input');
    input.type = 'file';
    input.accept = 'image/*';
    input.onchange = (event) => {
      const file = event.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = () => {
          const newStory = { id: Date.now(), image: reader.result, createdAt: Date.now() };
          stories.value.push(newStory);
          localStorage.setItem('stories', JSON.stringify(stories.value));
          cleanupStories();
        };
        reader.readAsDataURL(file);
      }
    };
    input.click();
  };
  
  const startStory = (index) => {
    activeStory.value = index;
    progress.value = 0;
    clearInterval(interval);
    interval = setInterval(() => {
      progress.value += 100 / 30;
      if (progress.value >= 100) {
        nextStory();
      }
    }, 100);
  };
  
  const nextStory = () => {
    if (activeStory.value < stories.value.length - 1) {
      startStory(activeStory.value + 1);
    } else {
      activeStory.value = null;
      clearInterval(interval);
    }
  };
  
  const cleanupStories = () => {
    const now = Date.now();
    stories.value = stories.value.filter(story => now - story.createdAt < 24 * 60 * 60 * 1000);
    localStorage.setItem('stories', JSON.stringify(stories.value));
  };
  
  onMounted(() => {
    cleanupStories();
  });
</script>




<template>
    <div class="w-full max-w-md mx-auto mt-5">
      <div class="flex items-center space-x-2 border p-2 rounded-md">
        <button @click="uploadImage" class="w-10 h-10 flex items-center justify-center border rounded-full">
          ➕
        </button>
        <div class="flex space-x-2 overflow-x-auto">
          <div v-for="(story, index) in stories" :key="story.id" @click="startStory(index)" class="w-10 h-10 rounded-full border overflow-hidden cursor-pointer">
            <img :src="story.image" class="w-full h-full object-cover" />
          </div>
        </div>
      </div>
  
      <div v-if="activeStory !== null" class="relative mt-4">
        <div class="absolute top-0 left-0 w-full h-1 bg-gray-200">
          <div class="h-full bg-blue-500" :style="{ width: progress + '%' }"></div>
        </div>
        <img :src="stories[activeStory].image" class="w-full h-64 object-cover rounded-md" />
      </div>
    </div>
  </template>
  
  <
  
  <style>
  ::-webkit-scrollbar {
    display: none;
  }
  </style>
  