<template>
  <div>
    <div :style="{'height': ht, 'width': wd, 'background': color}"></div>
  </div>
</template>

<script>
export default {
  // name: 'ComponentName',
  props: ['ht', 'wd', 'color'],
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
