<template>
  <div>
    <q-btn stack :ripple="false" flat>
      <q-icon top name="arrow_right_alt" size="140px" :style="{'color': color}"/>
      <div bottom class="text-center">
        <template v-if="label === 'BIN'">Binarization</template>
        <template v-if="label === 'SEG'">Image Segmentation</template>
        <template v-if="label === 'OCR'">OCR</template>
      </div>
    </q-btn>
  </div>
</template>

<script>
export default {
  // name: 'ComponentName',
  props: ['color', 'label'],
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
