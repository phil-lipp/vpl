<template>
  <div>
    <toolbar></toolbar>
    <lane></lane>
    <q-separator />
    <lane></lane>
  </div>
</template>

<style scoped>
</style>

<script>
import lane from '../components/lane'
import toolbar from '../components/toolbar'

export default {
  name: 'PageIndex',
  components: {
    lane,
    toolbar
  },
  data () {
    return {
      txt: ''
    }
  },
  methods: {
    dothis: function () {
      alert('Nicht zu hart!')
    }
  }
}
</script>
