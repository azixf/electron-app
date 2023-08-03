<template>
  <div class="calendar-contanier">
    <div class="valendar-header">
      <div class="prev"></div>
      <div>
        <span>{{ state.year }}</span
        >年 <span></span>月
      </div>
      <div class="next"></div>
    </div>
    <ul>
      <li v-for="month in state.months">
        <span>{{ numberToChinese(month.month) }}月</span>
      </li>
    </ul>
  </div>
</template>
<script setup lang="ts">
import { Solar, HolidayUtil, SolarYear } from 'lunar-typescript'

const now = Solar.fromDate(new Date())

const numberToChinese = computed(() => {
  return function (month: number): string {
    const mothCollections = new Map([
      [1, '一'],
      [2, '二'],
      [3, '三'],
      [4, '四'],
      [5, '五'],
      [6, '六'],
      [7, '七'],
      [8, '八'],
      [9, '九'],
      [10, '十'],
      [11, '十一'],
      [12, '十二']
    ])
    return mothCollections.get(month)!
  }
})

class Day {
  public day = ''
  public text = ''
  public isFestival = false
  public isToday = false
  public isHoliday = false
  public isRest = false
  public isCurrentMonth = false
}

class Month {
  public month = 0
  public days: Day[] = []
}

const state = reactive({
  year: now.getYear(),
  weekStart: 0,
  heads: ['一', '二', '三', '四', '五', '六', '日'],
  months: new Array<Month>()
})

function buildDay(d: Solar, month: number): Day {
  const lunar = d.getLunar()
  const day = new Day()
  day.day = d.getDay() + ''
  let text = lunar.getDayInChinese()
  if (1 === d.getDay()) {
    text = lunar.getMonthInChinese() + '月'
  }
  let festivals = d.getFestivals()
  if (festivals.length > 0) {
    text = festivals[0]
    day.isFestival = true
  }
  festivals = lunar.getFestivals()
  if (festivals.length > 0) {
    text = festivals[0]
    day.isFestival = true
  }
  const jq = lunar.getJieQi()
  if (jq) {
    text = jq
    day.isFestival = true
  }
  day.text = text
  if (d.toYmd() === now.toYmd()) {
    day.isToday = true
  }
  const h = HolidayUtil.getHoliday(d.getYear(), d.getMonth(), d.getDay())
  if (h) {
    day.isHoliday = true
    day.isRest = !h.isWork()
  }
  day.isCurrentMonth = d.getMonth() == month
  return day
}

function render(): void {
  const year = SolarYear.fromYear(parseInt(state.year + '', 10))
  const months: Month[] = []
  year.getMonths().forEach((m) => {
    const month = new Month()
    month.month = m.getMonth()
    const weeks = m.getWeeks(state.weekStart)
    weeks.forEach((w) => {
      w.getDays().forEach((d) => {
        month.days.push(buildDay(d, month.month))
      })
    })
    months.push(month)
  })
  state.months = months
}

render()
</script>
<style scoped lang="scss"></style>
