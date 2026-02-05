<template>
  <div class="bg-white rounded-lg shadow-md p-4 sm:p-6 w-full max-w-2xl h-full mt-8 sm:mt-12">
    <h3 class="text-lg font-semibold text-gray-800 mb-4">最近の日記</h3>
    <div v-if="recentDiaries.length === 0" class="text-gray-400 text-sm text-center py-8">
      まだ日記がありません
    </div>
    <div v-else class="space-y-3 max-h-64 overflow-y-auto">
      <button
        v-for="diary in recentDiaries"
        :key="diary.date"
        @click="goToDiary(diary.date)"
        class="w-full text-left p-3 rounded-lg hover:bg-gray-50 transition-colors border border-gray-100"
      >
        <div class="flex items-center justify-between mb-1">
          <span class="text-sm font-medium text-gray-900">
            {{ formatDate(diary.date) }}
          </span>
          <svg class="w-4 h-4 text-gray-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
          </svg>
        </div>
        <p v-if="diary.title" class="text-sm text-gray-600 truncate">
          {{ diary.title }}
        </p>
        <p v-else class="text-xs text-gray-400 italic">
          タイトルなし
        </p>
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import { useRouter } from 'vue-router'

interface Diary {
  date: string
  title: string
  content: string
}

const router = useRouter()
const recentDiaries = ref<Diary[]>([])

const loadRecentDiaries = () => {
  const diaries: Diary[] = []
  
  // localStorageから全ての日記を取得
  for (let i = 0; i < localStorage.length; i++) {
    const key = localStorage.key(i)
    if (key && key.startsWith('diary-')) {
      try {
        const diary = JSON.parse(localStorage.getItem(key) || '{}')
        diaries.push({
          date: key.replace('diary-', ''),
          title: diary.title || '',
          content: diary.content || '',
        })
      } catch (e) {
        console.error('日記の読み込みに失敗しました', e)
      }
    }
  }
  
  // 日付でソート（新しい順）
  diaries.sort((a, b) => b.date.localeCompare(a.date))
  
  // 最新5件を取得
  recentDiaries.value = diaries.slice(0, 5)
}

const formatDate = (dateString: string): string => {
  const date = new Date(dateString)
  const month = date.getMonth() + 1
  const day = date.getDate()
  const weekDay = ['日', '月', '火', '水', '木', '金', '土'][date.getDay()]
  return `${month}/${day}（${weekDay}）`
}

const goToDiary = (date: string) => {
  router.push(`/diary/${date}`)
}

onMounted(() => {
  loadRecentDiaries()
  
  // localStorageの変更を監視（別タブで保存された場合など）
  window.addEventListener('storage', loadRecentDiaries)
})

// コンポーネントが表示されている間、定期的に更新
const refreshInterval = setInterval(loadRecentDiaries, 1000)

onUnmounted(() => {
  window.removeEventListener('storage', loadRecentDiaries)
  clearInterval(refreshInterval)
})
</script>

