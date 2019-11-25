<template>
  <div>
    <button @click="addItem()">Add Component</button>
    <toolbar></toolbar>
    <lane></lane>
    <block @mousedown.native="moveStart($event, i-1)" @mouseup.native="moveEnd($event, i-1)" @mousemove.native="moveActive($event, i-1)"
      class="movable" :ref="'block'+(i-1)"
      :style="{'left': positions[i-1][0] + 'px', 'top': positions[i-1][1] + 'px'}" v-for="i in positions.length" :key="'block'+i"></block>
    <q-separator />
    <lane></lane>
  </div>
</template>

<style>
.movable {
  display: inline-block;
  padding: 0px;
  position: absolute;
}
</style>

<script>
import lane from '../components/lane'
import toolbar from '../components/toolbar'
import block from '../components/block'

export default {
  name: 'PageIndex',
  components: {
    lane,
    toolbar,
    block
  },
  data () {
    return {
      txt: '',
      moving: false,
      positions: [[200, 200]],
      color1: 'red'
    }
  },
  methods: {
    addItem: function () {
      this['positions'].push([400, 200])
    },
    moveStart: function (event, index) {
      this.moving = true
      this['offsetInitX' + index] = event.offsetX
      this['offsetInitY' + index] = event.offsetY
    },
    moveActive: function (event, index) {
      if (this.moving) {
        console.log(index + ' is moving')
        var posVar = 'positions'

        this['movingIndexCurrent'] = index

        var deltaX = event.offsetX - this['offsetInitX' + index]
        var deltaY = event.offsetY - this['offsetInitY' + index]

        console.log('I am here')

        console.log(posVar)
        this.$set(this[posVar][index], 0, this[posVar][index][0] + deltaX)
        this.$set(this[posVar][index], 1, this[posVar][index][1] + deltaY)
      }
    },
    moveEnd: function (event, index) {
      this.moving = false
    }
  }
}
</script>
