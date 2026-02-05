<template>
  <div class="min-h-screen !bg-gray-50 dark:!bg-gray-50 p-4 sm:p-8" style="color-scheme: light !important; background-color: #f9fafb !important;">
    <div class="max-w-4xl mx-auto">
      <!-- ヘッダー -->
      <div class="mb-6 sm:mb-8">
        <button
          @click="goBack"
          class="group flex items-center text-gray-600 hover:text-gray-900 transition-all duration-200 mb-4 sm:mb-6 px-3 py-2 rounded-lg hover:bg-white/50 backdrop-blur-sm"
        >
          <svg class="w-5 h-5 mr-2 transform group-hover:-translate-x-1 transition-transform" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
          </svg>
          <span class="font-medium">カレンダーに戻る</span>
        </button>
        <div class="!bg-white dark:!bg-white rounded-lg shadow-md p-6 border border-gray-200 dark:border-gray-200" style="background-color: white !important;">
          <div class="flex items-center gap-3 mb-2">
            <div class="w-8 h-8 bg-gradient-to-br from-blue-500 to-indigo-600 rounded-lg flex items-center justify-center shadow-md">
              <svg class="w-4 h-4 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z" />
              </svg>
            </div>
            <div>
              <h1 class="text-2xl sm:text-3xl font-bold bg-gradient-to-r from-gray-900 to-gray-700 bg-clip-text text-transparent">
                日記を書く
              </h1>
              <p class="text-sm sm:text-base text-gray-500 mt-1 font-medium">{{ formattedDate }}</p>
            </div>
          </div>
        </div>
      </div>

      <!-- フォーム -->
      <div class="!bg-gray-50 dark:!bg-gray-50 rounded-lg shadow-md p-6 sm:p-8 border border-gray-200 dark:border-gray-200" style="background-color: #f9fafb !important;">
        <form @submit.prevent="saveDiary" class="space-y-6">
          <!-- タイトル入力 -->
          <div class="space-y-2">
            <label for="title" class="block text-sm font-medium text-gray-700 mb-1">
              タイトル
            </label>
            <input
              id="title"
              v-model="diary.title"
              type="text"
              placeholder="今日のタイトルを入力..."
              class="w-full px-4 py-3 border-2 border-gray-700 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 outline-none transition-all duration-200 !bg-white dark:!bg-white !text-gray-900 dark:!text-gray-900 placeholder-gray-400 dark:placeholder-gray-400 shadow-sm"
              style="color-scheme: light !important; background-color: white !important; color: #111827 !important;"
            />
          </div>

          <!-- 本文入力 -->
          <div class="space-y-2">
            <label for="content" class="block text-sm font-medium text-gray-700 mb-1">
              本文
            </label>
            <textarea
              id="content"
              v-model="diary.content"
              rows="16"
              placeholder="今日の出来事や気持ちを自由に書いてください..."
              class="w-full px-4 py-3 border-2 border-gray-700 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 outline-none transition-all duration-200 !bg-white dark:!bg-white !text-gray-900 dark:!text-gray-900 placeholder-gray-400 dark:placeholder-gray-400 resize-none leading-relaxed shadow-sm"
              style="color-scheme: light !important; background-color: white !important; color: #111827 !important;"
            ></textarea>
            <p class="text-xs text-gray-400 text-right mt-1">
              {{ diary.content.length }} 文字
            </p>
          </div>

          <!-- ボタン -->
          <div class="flex flex-col sm:flex-row justify-end gap-3 pt-4 border-t border-gray-200">
            <button
              type="button"
              @click="goBack"
              class="px-4 py-2 border border-gray-300 rounded-lg text-gray-700 hover:bg-gray-50 hover:border-gray-400 transition-colors text-sm font-medium"
            >
              キャンセル
            </button>
            <button
              type="submit"
              :disabled="isSaving"
              class="px-5 py-2 bg-indigo-600 text-white rounded-lg hover:bg-indigo-700 transition-colors text-sm font-medium disabled:opacity-50 disabled:cursor-not-allowed flex items-center justify-center gap-2"
            >
              <svg v-if="isSaving" class="animate-spin h-4 w-4" fill="none" viewBox="0 0 24 24">
                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
              </svg>
              <svg v-else class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
              </svg>
              {{ isSaving ? '保存中...' : '保存する' }}
            </button>
          </div>
        </form>
      </div>

      <!-- トースト通知 -->
      <Transition
        enter-active-class="transition ease-out duration-300"
        enter-from-class="opacity-0 transform translate-y-2"
        enter-to-class="opacity-100 transform translate-y-0"
        leave-active-class="transition ease-in duration-200"
        leave-from-class="opacity-100"
        leave-to-class="opacity-0"
      >
        <div
          v-if="showToast"
          class="fixed bottom-4 right-4 bg-gradient-to-r from-green-500 to-emerald-600 text-white px-6 py-4 rounded-xl shadow-2xl flex items-center gap-3 z-50"
        >
          <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" />
          </svg>
          <span class="font-semibold">日記を保存しました！</span>
        </div>
      </Transition>
    </div>
  </div>
</template>

<script setup lang="ts">
interface Diary {
  title: string
  content: string
  date: string
}

const route = useRoute()
const router = useRouter()

const dateString = route.params.date as string

// 日付のフォーマット
const formattedDate = computed(() => {
  const date = new Date(dateString)
  const year = date.getFullYear()
  const month = date.getMonth() + 1
  const day = date.getDate()
  const weekDay = ['日', '月', '火', '水', '木', '金', '土'][date.getDay()]
  return `${year}年${month}月${day}日（${weekDay}）`
})

// 日記データ
const diary = ref<Diary>({
  title: '',
  content: '',
  date: dateString,
})

const isSaving = ref(false)
const showToast = ref(false)

// 既存の日記を読み込む
onMounted(() => {
  loadDiary()
})

const loadDiary = () => {
  const saved = localStorage.getItem(`diary-${dateString}`)
  if (saved) {
    try {
      const parsed = JSON.parse(saved)
      diary.value = { ...diary.value, ...parsed }
    } catch (e) {
      console.error('日記の読み込みに失敗しました', e)
    }
  }
}

const saveDiary = async () => {
  isSaving.value = true
  
  // 少し遅延を入れて保存中のアニメーションを見せる
  await new Promise(resolve => setTimeout(resolve, 500))
  
  // localStorageに保存
  localStorage.setItem(`diary-${dateString}`, JSON.stringify(diary.value))
  
  isSaving.value = false
  
  // トースト通知を表示
  showToast.value = true
  
  // 2秒後にカレンダーに戻る
  setTimeout(() => {
    router.push('/')
  }, 2000)
}

const goBack = () => {
  router.push('/')
}
</script>

