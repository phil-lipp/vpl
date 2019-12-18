<template>
  <div>
    <div :style="{'height': ht, 'width': wd, 'background': color}">
      <q-img
      :src="image"
      style="height: 180px; max-width: 120px"></q-img>
    </div>
  </div>
</template>

<script>
export default {
  // name: 'ComponentName',
  props: ['ht', 'wd', 'color', 'image'],
  data () {
    return {
    }
  },
  methods: {

  }
}
</script>

<style>
</style>
