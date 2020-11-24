<template>
  <div>
    <div class="max-w-screen-xl mx-auto py-6 sm:px-6 lg:px-8">
      <router-view />
    </div>
  </div>
</template>

<script>
import { defineComponent } from 'vue'
export default defineComponent({
  data: () => ({
    showMenu: false,
    showProfileMenu: false,
    links: [
      { text: 'Home of Fantasy Samajik Union ', to: '/' },
    ],
  }),
})
</script>
