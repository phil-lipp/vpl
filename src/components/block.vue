<template>
  <div>
    <div :style="{'height': ht, 'width': wd}">
      <q-img
      :src="image"
      style="height: 180px; max-width: 400px"
      contain></q-img>
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
