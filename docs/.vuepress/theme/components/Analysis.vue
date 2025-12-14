<template>
    <div class="busuanzi-stats" v-if="loaded">
      <!-- 站点统计（host） -->
      <div class="stats-line">
        <span id="busuanzi_container_site_pv">
          站点访问量：
          <span id="busuanzi_value_site_pv"></span>
        </span>
  
        <span class="divider">|</span>
  
        <span id="busuanzi_container_site_uv">
          站点访客数：
          <span id="busuanzi_value_site_uv"></span>
        </span>

        <span class="divider">|</span>

        <span id="busuanzi_container_page_pv">
          本页访问量：
          <span id="busuanzi_value_page_pv"></span>
        </span>
      </div>
  
    </div>
  </template>
  
  <script setup lang="ts">
  import { onMounted, watch, ref } from 'vue'
  import { useRoute } from 'vue-router'
  
  const loaded = ref(false)
  const route = useRoute()
  
  function loadBusuanzi() {
    if (typeof window === 'undefined') return
  
    // 防止重复加载脚本
    if (!document.getElementById('busuanzi-script')) {
      const script = document.createElement('script')
      script.id = 'busuanzi-script'
      script.async = true
      script.src = '//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js'
      script.onload = () => {
        loaded.value = true
      }
      document.body.appendChild(script)
    } else {
      loaded.value = true
      // @ts-ignore
      window.Busuanzi?.fetch()
    }
  }
  
  onMounted(loadBusuanzi)
  
  // 路由变化时，重新触发页面统计
  watch(
    () => route.fullPath,
    () => {
      // @ts-ignore
      window.Busuanzi?.fetch()
    }
  )
  </script>
  
  <style scoped>
  .busuanzi-stats {
    font-size: 14px;
    color: #666;
  }
  
  .stats-line {
    margin-bottom: 4px;
  }
  
  .divider {
    margin: 0 8px;
  }
  </style>
  