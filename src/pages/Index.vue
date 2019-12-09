<template>
  <q-page>
  <div>
    <toolbar @addcomp="addItem('Block')" @addlane="incrementLanes()" @addArrow="addItem('Arrow')"></toolbar>
    <lane v-for="i in lanecounter" :key="'lane'+i" :numb="i"></lane>
    <z-top>
      <block :ht="'100px'" :wd="'100px'" :color="'blue'" @mousedown.native="moveStart($event, 'Block', i-1)" @mouseup.native="moveEnd($event, 'Block', i-1)" @mousemove.native="moveActive($event, 'Block', i-1)"
      class="q-ma-md movable" :ref="'Block'+(i-1)"
      :style="{'left': positionsBlock[i-1][0] + 'px', 'top': positionsBlock[i-1][1] + 'px'}" v-for="i in positionsBlock.length" :key="'block'+i"></block>
      <arrow :color ="color" @mousedown.native="moveStart($event, 'Arrow', i-1)" @mouseup.native="moveEnd($event, 'Arrow', i-1)" @mousemove.native="moveActive($event, 'Arrow', i-1)"
      class="q-ma-md movable" :ref="'Arrow'+(i-1)"
      :style="{'left': positionsArrow[i-1][0] + 'px', 'top': positionsArrow[i-1][1] + 'px'}" v-for="i in positionsArrow.length" :key="'arrow'+i"></arrow>
    </z-top>
  </div>
  </q-page>
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
import arrow from '../components/arrow'

export default {
  name: 'PageIndex',
  components: {
    lane,
    toolbar,
    block,
    arrow
  },
  data () {
    return {
      txt: '',
      moving: false,
      positionsBlock: [[200, 200]],
      positionsArrow: [[280, 180]],
      color: 'purple',
      lanecounter: 1
    }
  },
  methods: {
    addItem: function (type) {
      // Read this:
      // https://vuejs.org/2016/02/06/common-gotchas/
      if (type === 'Block') {
        this['positions' + type].push([400, 200])
      } else {
        this['positions' + type].push([480, 180])
      }
    },

    incrementLanes: function () {
      this.lanecounter += 1
    },

    moveStart: function (event, type, index) {
      this.moving = true
      this['offsetInitX' + type + index] = event.offsetX
      this['offsetInitY' + type + index] = event.offsetY
    },

    moveActive: function (event, type, index) {
      if (this.moving) {
        console.log(type + ' of index ' + index + ' is moving')
        var posVar = 'positions' + type

        this['movingTypeCurent'] = type
        this['movingIndexCurrent'] = index

        var deltaX = event.offsetX - this['offsetInitX' + type + index]
        var deltaY = event.offsetY - this['offsetInitY' + type + index]

        console.log('I am here')

        // update the positions of the element in that specific index
        // we use this.$set because vue cannot track simple array assignments
        // https://vuejs.org/2016/02/06/common-gotchas/

        console.log(posVar)
        this.$set(this[posVar][index], 0, this[posVar][index][0] + deltaX)
        this.$set(this[posVar][index], 1, this[posVar][index][1] + deltaY)

        // Check for collision

        var boundBox1

        if (typeof this.$refs[type + index].$el === 'undefined') {
          boundBox1 = this.$refs[type + index][0].$el.getBoundingClientRect()
        } else {
          boundBox1 = this.$refs[type + index].$el.getBoundingClientRect()
        }

        for (var i = 0; i < this.positionsBlock.length; i++) {
          if (type === 'Arrow') {
            let boundBox2

            // Get the bounding box of the other elements
            if (typeof this.$refs['Block' + i].$el === 'undefined') {
              boundBox2 = this.$refs['Block' + i][0].$el.getBoundingClientRect()
            } else {
              boundBox2 = this.$refs['Block' + i].$el.getBoundingClientRect()
            }

            var overlap = !(boundBox1.right < boundBox2.left ||
                            boundBox1.left > boundBox2.right ||
                            boundBox1.bottom < boundBox2.top ||
                            boundBox1.top > boundBox2.bottom)

            if (overlap) {
              console.log(type + ' of index ' + index + ' collided of index ' + i)
              this['color'] = 'green'
            } else {
              this['color'] = 'purple'
            }
          }
        }
      }
    },

    moveEnd: function (event, type, index) {
      this.moving = false
    }
  }
}
</script>
