<script setup lang="ts">
  defineProps<{
    show: any;
  }>();
</script>

<template>
  <div class="w-64 bg-white border border-gray-200 rounded-lg shadow dark:bg-gray-800 dark:border-gray-700 cursor-pointer">
    <a>
      <img class="rounded-t-lg object-cover w-full h-56" :src="show?.image?.medium" alt="" />
    </a>
    <div class="px-5 pt-5">
      <a>
        <h5 class="h-16 mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white line-clamp-2">{{ show?.name }}</h5>
      </a>
      <p class="mb-3 font-normal text-gray-700 dark:text-gray-400 overflow-hidden line-clamp-6" v-html="show?.summary"></p>
    </div>
  </div>
</template>
