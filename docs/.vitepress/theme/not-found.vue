<template>
  <div>

  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { withBase, useRouter } from 'vitepress'
const root = ref('/')
const router = useRouter()
onMounted(() => {
  onMounted(()=>{
    router.go(withBase('/'))
  })
})
</script>

<style scoped>

</style>