<template>
  <div class="dash-page">
    <Card>
      <Calendar />
    </Card>
  </div>
</template>
<script lang="ts" setup>
const Calendar = defineAsyncComponent(() => import('@/components/dashboard/calendar.vue'))
</script>
<style lang="scss" scoped>
.dash-page {
  display: grid;
  padding-top: $padding-mini;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: auto;
  grid-auto-rows: minmax(80px, auto);
  gap: 10px;
}
</style>
