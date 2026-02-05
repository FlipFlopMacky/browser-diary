<template>
  <div class="h-screen bg-gray-50 flex flex-col overflow-hidden">
    <div class="max-w-6xl mx-auto w-full flex flex-col flex-1 p-4 sm:p-8">
      <h1 class="text-2xl sm:text-4xl font-bold text-gray-900 mb-4 sm:mb-6 flex-shrink-0">
        ブラウザ日記
      </h1>
      <div class="flex-1 grid grid-cols-1 lg:grid-cols-2 gap-4 sm:gap-6 min-h-0">
        <div class="flex items-center justify-center min-h-0">
          <Calendar @date-selected="handleDateSelected" />
        </div>
        <div class="flex items-start justify-center min-h-0 w-full">
          <RecentDiaries ref="recentDiariesRef" />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const router = useRouter()
const recentDiariesRef = ref()

const handleDateSelected = (date: Date) => {
  // 日付をYYYY-MM-DD形式に変換
  const dateString = date.toISOString().split('T')[0]
  // 日記入力ページに遷移
  router.push(`/diary/${dateString}`)
}
</script>

