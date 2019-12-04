<template>
  <q-page>
  <div>
    <toolbar @addcomp="addItem()" @addlane="incrementLanes()" @addArrow="addArrow()" @undoAction="undo()" @redoAction="redo()">
    </toolbar>
    <lane v-for="i in lanecounter" :key="'lane'+i" :numb="i"></lane>
    <z-top>
      <block :ht="'100px'" :wd="'100px'" :color="'blue'" @mousedown.native="moveStart($event, i-1)" @mouseup.native="moveEnd($event, i-1)" @mousemove.native="moveActive($event, i-1)"
      class="q-ma-md movable" :ref="'block'+(i-1)"
      :style="{'left': positions[i-1][0] + 'px', 'top': positions[i-1][1] + 'px'}" v-for="i in positions.length" :key="'block'+i"></block>
      <arrow @mousedown.native="arrMoveStart($event, i-1)" @mouseup.native="arrMoveEnd($event, i-1)" @mousemove.native="arrMoveActive($event, i-1)"
      class="q-ma-md movable" :ref="'arrow'+(i-1)"
      :style="{'left': arrPositions[i-1][0] + 'px', 'top': arrPositions[i-1][1] + 'px'}" v-for="i in arrPositions.length" :key="'arrow'+i"></arrow>
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
      positions: [[200, 200]],
      arrPositions: [[280, 180]],
      color1: 'red',
      lanecounter: 1
    }
  },
  methods: {
    addItem: function () {
      this['positions'].push([400, 200])
    },

    incrementLanes: function () {
      this.lanecounter += 1
    },

    addArrow: function () {
      this['arrPositions'].push([480, 180])
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
    },

    arrMoveStart: function (event, index) {
      this.moving = true
      this['offsetInitX' + index] = event.offsetX
      this['offsetInitY' + index] = event.offsetY
    },

    arrMoveActive: function (event, index) {
      if (this.moving) {
        console.log(index + ' is moving')
        var posVar = 'arrPositions'

        this['movingIndexCurrent'] = index

        var deltaX = event.offsetX - this['offsetInitX' + index]
        var deltaY = event.offsetY - this['offsetInitY' + index]

        console.log('I am here')

        console.log(posVar)
        this.$set(this[posVar][index], 0, this[posVar][index][0] + deltaX)
        this.$set(this[posVar][index], 1, this[posVar][index][1] + deltaY)
      }
    },

    arrMoveEnd: function (event, index) {
      this.moving = false
    },
    undo () {
      // shift the stack
      const data = this.undoStack.shift()
      if (data !== void 0) {
        // block undo from receiving its own data
        this.undoBlocked = true
        this.$refs.editor.innerText = data
      }
    },

    redo () {
      // shift the stack
      const data = this.redoStack.shift()
      if (data !== void 0) {
        // unblock undo from receiving redo data
        this.undoBlocked = false
        this.$refs.editor.innerText = data
      }
    },

    handler (mutationRecords) {
      mutationRecords.forEach(record => {
        if (record.type === 'characterData') {
          this.undoStack.unshift(record.oldValue)
          this.checkStack(this.undoStack)
          this.clearStack(this.redoStack)
        } else if (record.type === 'childList') {
          record.removedNodes.forEach(node => {
            if (this.undoBlocked === false) {
              // comes from redo
              this.undoStack.unshift(node.textContent)
            } else {
              // comes from undo
              this.redoStack.unshift(node.textContent)
            }
          })

          // check stacks
          this.checkStack(this.undoStack)
          this.checkStack(this.redoStack)
          this.undoBlocked = false
        }
      })
    },

    checkStack (stack) {
      if (stack.length > this.maxStack) {
        stack.splice(this.maxStack)
      }
    },

    clearStack (stack) {
      stack.splice(0)
    }
  }
}
</script>
