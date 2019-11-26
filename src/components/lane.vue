<template>
  <div class="q-pa-md">
    <text-caption>Lane {{numb}}</text-caption>
    <q-scroll-area horizontal style="height: 210px;" class="bg-grey-3">
    </q-scroll-area>
  </div>
</template>

<style scoped>
</style>

<script>
export default {
  // name: 'ComponentName',
  props: ['numb'],
  data () {
    return {
      laneInhalt: ''
    }
  }
}
</script>
