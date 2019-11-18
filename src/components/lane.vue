<template>
  <div class="q-pa-md">
    <q-scroll-area
      horizontal
      style="height: 210px; width: 1230px;"
      class="bg-grey-3"
    >
      <div class="row no-wrap">
        <div class="q-pa-sm">
          <q-img src="https://placeimg.com/500/300/nature"/>
        </div>
      </div>
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
