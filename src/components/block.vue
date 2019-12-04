<template>
  <div>
    <div :style="{'height': ht, 'width': wd, 'background': color}">
      <q-img
      src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/9f/Codex_Manesse_127r.jpg/220px-Codex_Manesse_127r.jpg"
      style="height: 100px; max-width: 100px"></q-img>
    </div>
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
