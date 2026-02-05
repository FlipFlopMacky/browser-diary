<template>
  <div class="bg-white rounded-lg shadow-md p-3 sm:p-6 w-full max-w-2xl flex flex-col h-full max-h-full">
    <div class="flex items-center justify-between mb-3 sm:mb-4 flex-shrink-0">
      <button
        @click="previousMonth"
        class="p-1 sm:p-2 hover:bg-gray-100 rounded-lg transition-colors"
        aria-label="前の月"
      >
        <svg class="w-4 h-4 sm:w-5 sm:h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
        </svg>
      </button>
      <h2 class="text-lg sm:text-2xl font-semibold text-gray-800">
        {{ currentYear }}年 {{ currentMonth }}月
      </h2>
      <button
        @click="nextMonth"
        class="p-1 sm:p-2 hover:bg-gray-100 rounded-lg transition-colors"
        aria-label="次の月"
      >
        <svg class="w-4 h-4 sm:w-5 sm:h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
        </svg>
      </button>
    </div>

    <div class="grid grid-cols-7 gap-1 sm:gap-2 mb-1 sm:mb-2 flex-shrink-0">
      <div
        v-for="day in weekDays"
        :key="day"
        class="text-center text-xs sm:text-sm font-medium text-gray-600 py-1 sm:py-2"
      >
        {{ day }}
      </div>
    </div>

    <div class="grid grid-cols-7 gap-1 sm:gap-2 flex-1 min-h-0">
      <div
        v-for="(date, index) in calendarDays"
        :key="index"
        @click="selectDate(date)"
        :class="[
          'flex items-center justify-center rounded-lg cursor-pointer transition-colors text-xs sm:text-base',
          date === null ? 'bg-transparent' : '',
          date && isToday(date) ? 'bg-blue-500 text-white font-semibold' : '',
          date && !isToday(date) && isSelected(date) ? 'bg-blue-100 text-blue-700' : '',
          date && !isToday(date) && !isSelected(date) ? 'hover:bg-gray-100 text-gray-700' : '',
          date && !isCurrentMonth(date) ? 'text-gray-400' : '',
        ]"
      >
        <span v-if="date">{{ date.getDate() }}</span>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

const weekDays = ['日', '月', '火', '水', '木', '金', '土']

const currentDate = ref(new Date())
const selectedDate = ref<Date | null>(null)

const currentYear = computed(() => currentDate.value.getFullYear())
const currentMonth = computed(() => currentDate.value.getMonth() + 1)

const calendarDays = computed(() => {
  const year = currentDate.value.getFullYear()
  const month = currentDate.value.getMonth()
  
  // 月の最初の日
  const firstDay = new Date(year, month, 1)
  // 月の最後の日
  const lastDay = new Date(year, month + 1, 0)
  
  // 最初の日の曜日（0=日曜日）
  const startDay = firstDay.getDay()
  
  // カレンダーに表示する日付の配列
  const days: (Date | null)[] = []
  
  // 前月の空白を追加
  for (let i = 0; i < startDay; i++) {
    days.push(null)
  }
  
  // 今月の日付を追加
  for (let day = 1; day <= lastDay.getDate(); day++) {
    days.push(new Date(year, month, day))
  }
  
  return days
})

const isToday = (date: Date): boolean => {
  const today = new Date()
  return (
    date.getDate() === today.getDate() &&
    date.getMonth() === today.getMonth() &&
    date.getFullYear() === today.getFullYear()
  )
}

const isSelected = (date: Date): boolean => {
  if (!selectedDate.value) return false
  return (
    date.getDate() === selectedDate.value.getDate() &&
    date.getMonth() === selectedDate.value.getMonth() &&
    date.getFullYear() === selectedDate.value.getFullYear()
  )
}

const isCurrentMonth = (date: Date): boolean => {
  return date.getMonth() === currentDate.value.getMonth()
}

const previousMonth = () => {
  const newDate = new Date(currentDate.value)
  newDate.setMonth(newDate.getMonth() - 1)
  currentDate.value = newDate
}

const nextMonth = () => {
  const newDate = new Date(currentDate.value)
  newDate.setMonth(newDate.getMonth() + 1)
  currentDate.value = newDate
}

const selectDate = (date: Date | null) => {
  if (date) {
    selectedDate.value = date
    // 日付選択時のイベントを発火（必要に応じて親コンポーネントに通知）
    emit('date-selected', date)
  }
}

const emit = defineEmits<{
  'date-selected': [date: Date]
}>()
</script>

