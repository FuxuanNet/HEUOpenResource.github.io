<script setup lang="ts">
  import { computed, onMounted, ref } from 'vue'
  import { VPLink } from 'vuepress-theme-plume/client'
  import { useRouteLocale } from 'vuepress/client'
  
  interface Locale {
    star: string
    issue: string
    sponsor: string
  }
  
  const locales: Record<string, Locale> = {
    '/': { star: '在 GitHub 上 Star', issue: '遇到问题？', sponsor: '喝杯奶茶' },
  }
  
  const lang = useRouteLocale()
  const locale = computed(() => locales[lang.value])
  
  interface RepoCommitInfo {
    label: string
    time: string
    url: string
  }
  
  const commits = ref<RepoCommitInfo[]>([])
  
  async function fetchLatestCommit(
  owner: string,
  repo: string,
  branch: string,
  label: string,
) {
  const api = `https://api.github.com/repos/${owner}/${repo}/commits/${branch}`

  try {
    const res = await fetch(api)
    const data = await res.json()

    // 如果请求失败或返回错误信息
    if (res.status !== 200 || !data || !data.commit) {
      console.warn(`无法获取 ${owner}/${repo} ${branch} 的最新提交`, data)
      return {
        label,
        time: '未知时间',
        url: '#',
      }
    }

    // 优先取 committer.date，没有则取 author.date
    const commitDate = data.commit.committer?.date || data.commit.author?.date
    const commitUrl = data.html_url || '#'

    return {
      label,
      time: commitDate ? new Date(commitDate).toLocaleString() : '未知时间',
      url: commitUrl,
    }
  } catch (err) {
    console.error(`获取 ${owner}/${repo} ${branch} 提交时出错`, err)
    return {
      label,
      time: '未知时间',
      url: '#',
    }
  }
}

  
  onMounted(async () => {
    commits.value = await Promise.all([
      fetchLatestCommit(
        'HEUOpenResource',
        'heu-icicles',
        'main',
        '资料更新：',
      ),
      fetchLatestCommit(
        'HEUOpenResource',
        'HEUOpenResource.github.io',
        'gh-pages',
        '网站更新：',
      ),
    ])
  })
  </script>
  
    
    <template>
      <div class="aside-github-star">
        <a href="https://github.com/HEUOpenResource/heu-icicles" target="_blank" class="github-stats-link">
      <div class="stats-item">
        <img src="https://img.shields.io/github/stars/HEUOpenResource/heu-icicles?style=for-the-badge&label=⭐ Stars" alt="Stars">
      </div>
      <div class="stats-item">
        <img src="https://img.shields.io/github/forks/HEUOpenResource/heu-icicles?style=for-the-badge&label=🔱 Forks" alt="Forks">
      </div>
    </a>
      </div>

      <div class="aside-commit-wrapper">
        <a
          v-for="item in commits"
          :key="item.label"
          :href="item.url"
          target="_blank"
          class="commit-item"
        >
          <span class="commit-name">{{ item.label }}</span>
          <span class="commit-time">{{ item.time }}</span>
        </a>
      </div>

      <div class="aside-nav-wrapper">
        <VPLink class="link" no-icon href="https://github.com/HEUOpenResource/heu-icicles">
          <span class="vpi-github-star" />
          <span class="link-text">{{ locale.star }}</span>
          <span class="vpi-arrow-right" />
        </VPLink>
        <VPLink class="link" no-icon href="https://github.com/orgs/HEUOpenResource/discussions">
          <span class="vpi-github-issue" />
          <span class="link-text">{{ locale.issue }}</span>
          <span class="vpi-arrow-right" />
        </VPLink>
      </div>
    </template>
    
    <style scoped>
    .aside-nav-wrapper {
      display: flex;
      flex-direction: column;
      padding: 8px 0;
      margin: 8px 16px 0;
      border-top: solid 1px var(--vp-c-divider);
    }
    
    .aside-nav-wrapper .link {
      display: flex;
      gap: 8px;
      align-items: center;
      font-size: 14px;
      color: var(--vp-c-text-2);
      transition: color var(--vp-t-color);
    }
    
    .aside-nav-wrapper .link:hover {
      color: var(--vp-c-brand-1);
    }
    
    .aside-nav-wrapper .link .link-text {
      flex: 1 2;
      font-size: 12px;
    }
    
    .vpi-github-star {
      --icon: url("data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyNCIgaGVpZ2h0PSIyNCIgdmlld0JveD0iMCAwIDI0IDI0Ij48cGF0aCBmaWxsPSJjdXJyZW50Q29sb3IiIGQ9Ik0xOSAxNXEuMy0uMy43MTMtLjN0LjcxMi4zTDIyIDE2LjZxLjMuMy4zLjd0LS4zLjd0LS43LjN0LS43LS4zTDE5IDE2LjQyNXEtLjMtLjMtLjMtLjcxMlQxOSAxNW0xLTEycS4zLjMuMy43MTN0LS4zLjcxMkwxOC40MjUgNnEtLjMuMy0uNzEyLjNUMTcgNnQtLjMtLjcxMnQuMy0uNzEzTDE4LjYgM3EuMy0uMy43LS4zdC43LjNNNCAzcS4zLS4zLjcxMy0uM3QuNzEyLjNMNyA0LjZxLjMuMy4zLjdUNyA2dC0uNzEyLjN0LS43MTMtLjNMNCA0LjQyNXEtLjMtLjMtLjMtLjcxMlQ0IDNtMSAxMnEuMy4zLjMuNzEzdC0uMy43MTJMMy40MjUgMThxLS4zLjMtLjcxMi4zVDIgMTh0LS4zLS43MTJ0LjMtLjcxM0wzLjYgMTVxLjMtLjMuNy0uM3QuNy4zbTMuODUgMS44MjVsMy4xNS0xLjlsMy4xNSAxLjkyNWwtLjgyNS0zLjZsMi43NzUtMi40bC0zLjY1LS4zMjVsLTEuNDUtMy40bC0xLjQ1IDMuMzc1bC0zLjY1LjMyNWwyLjc3NSAyLjQyNXptMy4xNS40NWwtNC4xNSAyLjVxLS4yNzUuMTc1LS41NzUuMTV0LS41MjUtLjJ0LS4zNS0uNDM3dC0uMDUtLjU4OGwxLjEtNC43MjVMMy43NzUgMTAuOHEtLjI1LS4yMjUtLjMxMi0uNTEzdC4wMzctLjU2MnQuMy0uNDV0LjU1LS4yMjVsNC44NS0uNDI1bDEuODc1LTQuNDVxLjEyNS0uMy4zODgtLjQ1dC41MzctLjE1dC41MzcuMTV0LjM4OC40NWwxLjg3NSA0LjQ1bDQuODUuNDI1cS4zNS4wNS41NS4yMjV0LjMuNDV0LjAzOC41NjN0LS4zMTMuNTEybC0zLjY3NSAzLjE3NWwxLjEgNC43MjVxLjA3NS4zMjUtLjA1LjU4OHQtLjM1LjQzN3QtLjUyNS4ydC0uNTc1LS4xNXoiLz48L3N2Zz4=")}
    
    .vpi-github-issue {
      --icon: url("data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyNCIgaGVpZ2h0PSIyNCIgdmlld0JveD0iMCAwIDI0IDI0Ij48ZyBmaWxsPSJub25lIiBmaWxsLXJ1bGU9ImV2ZW5vZGQiIGNsaXAtcnVsZT0iZXZlbm9kZCI+PHBhdGggZmlsbD0iIzBjNmZmZiIgZD0iTTkuODU4IDE0Ljc2YzIuNTcuODkgNi4xMjIgMCA3LjQzMy0yLjkyMWE1LjY2NCA1LjY2NCAwIDAgMC02LjA4Mi03LjgyNGE1LjY2IDUuNjYgMCAwIDAtNC43MzMgNC44MjNjLS4xOSAxLjU1LjI1IDMuNzcxIDEuNjUgNC42ODJhLjMwMy4zMDMgMCAwIDAgLjM0LS41Yy0xLjEzLS43OS0xLjQxLTIuNzUyLTEuMTgtNC4wNzJhNC43NDIgNC43NDIgMCAxIDEgOS4xMDUgMi41M2MtMSAyLjQ5Mi00LjEwMiAzLjM2Mi02LjMyMyAyLjY3MmEuMzQuMzQgMCAwIDAtLjIxLjYxIi8+PHBhdGggZmlsbD0iIzAyMDIwMiIgZD0iTTIzLjY0NCA1LjgxNmMtLjEzLTEuMS0uMDctMy4yNzEtLjkyLTMuODcyQzIwLjgxMi41ODQgMTYuNjYuOTU0IDEzLjkxLjg1NGMtMy41MTItLjEyLTcuMTQ0LjE2LTEwLjYyNS4yNWEuMzQuMzQgMCAxIDAgMCAuNjdjMy40NzEtLjA1IDcuMDYzLS4yNiAxMC41ODUtLjA4YzIuNDguMTMgNi42NzMtLjEzIDguMjYzIDFjLjM4LjI5LjM5IDIuNDQyLjQ3IDMuMjAyYTM1IDM1IDAgMCAxIC4wNyA4LjEwNGMtLjA0Ny44OTQtLjE5NSAxLjc4LS40NCAyLjY0MWMtMi40NSAxLjI4LTE5Ljc0OSAyLjE4MS0yMC43Ni41NmExNy4zIDE3LjMgMCAwIDEtLjY0LTQuMTUxYTYwIDYwIDAgMCAxIC42LTEwLjg0NmEuMzExLjMxMSAwIDEgMC0uNDQtLjQ0Yy0uMzkuMzktLjMxIDEuNjQxLS4zOCAyLjA3MWE0NCA0NCAwIDAgMC0uNTIgMTAuMTg1Yy4wNDggMS4yNDguMjUzIDIuNDg1LjYxIDMuNjgyYy44NCAxLjQxIDYuMzEzIDEuNDYgOC4wMDQgMS40N2E0LjMgNC4zIDAgMCAwLS44OSAzLjAwMmMtLjc3IDAtMS40OTEuMS0yLjE2MS4xNmEuMy4zIDAgMSAwIDAgLjU5YzIgLjA3IDMuNTQxLjI3IDYuMDAzLjI1YzIuNDYtLjAyIDQuMDcxLS4yMyA2LjA3Mi0uMzhhLjM0LjM0IDAgMCAwIDAtLjY3aC0xLjg0YTguOCA4LjggMCAwIDAtMS4wMDEtMy4xNTJjMS4zMi0uMSA3LjU1My0uNTMgOC4yOTQtMS42M2E4LjcgOC43IDAgMCAwIC42NC0zLjE2MmEzNCAzNCAwIDAgMC0uMTgtOC4zNjRNMTUuMTIgMjIuMTI0Yy0yLjc0MS0uMDYtNC4xOTItLjA5LTYuNTUzIDBjLjE2LS4zOS4zOS0uOS41MS0xLjI5cS4yMTctLjgxNC4zLTEuNjUyYzAtLjEzLS4yNC4xMSA0LjY4My0uMTlhNi44IDYuOCAwIDAgMCAxLjA2IDMuMTMyIi8+PHBhdGggZmlsbD0iIzAyMDIwMiIgZD0iTTEzLjg4IDguNjM3YTEuNzIyIDEuNzIyIDAgMCAwLTIuMDAxLTJjLTEuNzQxLjIzLTIuMDcxIDIuNDQtMSAyLjMxYS4yOTEuMjkxIDAgMCAwIC4xMi0uNTdhLjUzLjUzIDAgMCAxIC4xOC0uNDJhMSAxIDAgMCAxIDEuNDYgMGMuMzcuNCAwIC44OS0uNDUgMS40NmMtLjE0LjE3LS43NC42NzEtLjY5IDEuMDUxYy4wNy42MiAxIC41Mi44OSAwYy4xOC0uMTEuMzctLjQzLjQ0LS40OWEzLjUgMy41IDAgMCAwIDEuMDUtMS4zNG0tMS45NDEgMi43NDFjLS44Ny0uMDctMS4yMSAxLjYuMDcgMS41MWMuMjEgMCAuNTMtLjA4LjY2LS4zNmEuNzIuNzIgMCAwIDAgLjA1LS42YTEgMSAwIDAgMC0uNzgtLjU1Ii8+PC9nPjwvc3ZnPg==") 
    }
    
    /* 徽章容器 - 修改这里：移除上边距，只保留左右边距 */
    .aside-github-star {
      padding: 8px 16px 0; /* 上内边距8px，左右内边距16px，下内边距0 */
      margin: 8px 0px 0;
      border-top: solid 1px var(--vp-c-divider);
    }

    .github-stats-link {
      display: flex;
      gap: 8px;
      text-decoration: none;
    }

    .stats-item {
      flex: 1;
      text-align: center;
    }

    .stats-item img {
      width: 100%;
      max-width: 150px;
      height: auto;
      border-radius: 6px;
      transition: all 0.2s ease;
    }

    /* .stats-item img:hover {
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
      transform: translateY(-2px);
    } */
    .aside-commit-wrapper {
      margin: 8px 16px 0;
      padding-top: 8px;
      border-top: solid 1px var(--vp-c-divider);
      display: flex;
      flex-direction: column;
      gap: 6px;
    }

    .commit-title {
      font-size: 12px;
      color: var(--vp-c-text-3);
    }

    .commit-item {
      display: flex;
      justify-content: space-between;
      gap: 8px;
      font-size: 12px;
      color: var(--vp-c-text-2);
      text-decoration: none;
    }

    .commit-item:hover {
      color: var(--vp-c-brand-1);
    }

    .commit-time {
      white-space: nowrap;
      color: var(--vp-c-text-3);
    }

    </style>
    