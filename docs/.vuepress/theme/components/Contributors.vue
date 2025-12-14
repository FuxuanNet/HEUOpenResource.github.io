<script setup lang="ts">
  import { computed } from 'vue'
  
  type GitHubContributor = string
  
  type CustomContributor = {
    name: string
    avatar: string
    url: string
  }
  
  type Contributor = GitHubContributor | CustomContributor
  
  const props = defineProps<{
    contributors: Contributor[]
  }>()
  
  const list = computed(() =>
    props.contributors.map(c => {
      // GitHub：只传用户名
      if (typeof c === 'string') {
        return {
          name: c,
          avatar: `https://avatars.githubusercontent.com/${c}?v=4`,
          url: `https://github.com/${c}`,
        }
      }
  
      // 非 GitHub：完全使用 frontmatter 提供的数据
      return c
    }),
  )
  </script>
  
    
    <template>
      <div class="contributors">
        <div
          v-for="contributor in list"
          :key="contributor.url"
          class="contributor"
        >
          <a
            :href="contributor.url"
            target="_blank"
            rel="noopener noreferrer"
          >
            <img
              :src="contributor.avatar"
              :alt="contributor.name"
              loading="lazy"
              class="no-view"
              width="460"
              height="460"
            >
          </a>
    
          <a
            :href="contributor.url"
            target="_blank"
            rel="noopener noreferrer"
          >
            {{ contributor.name }}
          </a>
        </div>
      </div>
    </template>
    
    
    <style scoped>
    .contributors {
      display: flex;
      flex-wrap: wrap;
      gap: 24px;
      align-items: center;
      justify-content: flex-start;
      margin-top: 32px;
      margin-bottom: 64px;
    }
    
    .contributor {
      display: flex;
      gap: 8px;
      align-items: center;
    }
    
    .contributor img {
      width: 32px;
      height: 32px;
      cursor: default;
      border-radius: 100%;
    
      object-fit: contain;
    }
    
    .contributor a {
      text-decoration: none;
    }
    </style>