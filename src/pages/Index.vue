<template>
  <q-page>
  <div>
    <toolbar @addlane="incrementLanes()" @addBIN="addArrow('BIN')" @addIMGSEG="addArrow('SEG')" @addOCR="addArrow('OCR')" @image1="addIMG(1)" @image2="addIMG(2)" @image3="addIMG(3)"
    @ownIMG="addOwnIMG"></toolbar>
    <lane v-for="i in lanecounter" :key="'lane'+i" :numb="i"></lane>
    <z-top>
      <block :ht="'100px'" :wd="'120px'" :color="'blue'" :image="images[i-1]" :blockArt="blockType[i-1]" @mousedown.native="moveStart($event, 'Block', i-1)" @mouseup.native="moveEnd($event, 'Block', i-1)" @mousemove.native="moveActive($event, 'Block', i-1)"
      class="q-ma-md movable" :ref="'Block'+(i-1)"
      :style="{'left': positionsBlock[i-1][0] + 'px', 'top': positionsBlock[i-1][1] + 'px'}" v-for="i in positionsBlock.length" :key="'block'+i"></block>
      <arrow :color ="color[i-1]" :label="arrowType[i-1]" @mousedown.native="moveStart($event, 'Arrow', i-1)" @mouseup.native="moveEnd($event, 'Arrow', i-1)" @mousemove.native="moveActive($event, 'Arrow', i-1)"
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
      positionsBlock: [],
      positionsArrow: [],
      blocksMovable: [],
      arrowMovable: [],
      arrowType: [],
      blockType: [],
      url1: 'statics/img1.jpg',
      url2: 'statics/img2.jpg',
      url3: 'statics/img3.jpg',
      urlb1: 'statics/img1bin.jpg',
      urlb2: 'statics/img2bin.jpg',
      urlb3: 'statics/img3bin.jpg',
      urls1: 'statics/img1seg.jpg',
      urls2: 'statics/img2seg.jpg',
      urls3: 'statics/img3seg.jpg',
      images: [],
      color: [],
      lanecounter: 1
    }
  },
  methods: {
    addArrow: function (type) {
      // Read this:
      // https://vuejs.org/2016/02/06/common-gotchas/
      this['positionsArrow'].push([480, 180])
      this['arrowMovable'].push(true)
      if (type === 'BIN') {
        this['color'].push('purple')
        this['arrowType'].push('BIN')
      } else if (type === 'SEG') {
        this['color'].push('orange')
        this['arrowType'].push('SEG')
      } else if (type === 'OCR') {
        this['color'].push('turquoise')
        this['arrowType'].push('OCR')
      }
    },

    incrementLanes: function () {
      this.lanecounter += 1
    },

    moveStart: function (event, type, index) {
      if (type === 'Block') {
        if (this['blocksMovable'][index] === true) {
          this.moving = true
          this['offsetInitX' + type + index] = event.offsetX
          this['offsetInitY' + type + index] = event.offsetY
        }
      } else if (type === 'Arrow') {
        if (this['arrowMovable'][index] === true) {
          this.moving = true
          this['offsetInitX' + type + index] = event.offsetX
          this['offsetInitY' + type + index] = event.offsetY
        }
      } else {

      }
    },

    moveActive: function (event, type, index) {
      if ((type === 'Block' && this['blocksMovable'][index] === true) || (type === 'Arrow' && this['arrowMovable'][index] === true)) {
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

              if (this['arrowMovable'][index] === true) {
                if (overlap) {
                  console.log(type + ' of index ' + index + ' collided of index ' + i)
                  this['color'][index] = 'green'
                  if (this['images'][i] === this['url1']) {
                    this['arrowMovable'][index] = false
                    this['blocksMovable'][i] = false
                    this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1]])
                    this['blocksMovable'].push(true)
                    this.moving = false
                    if (this['arrowType'][index] === 'BIN') {
                      this['images'].push(this['urlb1'])
                      this['blockType'].push('image')
                    }
                    if (this['arrowType'][index] === 'SEG') {
                      this['images'].push(this['urls1'])
                      this['blockType'].push('image')
                    }
                    if (this['arrowType'][index] === 'OCR') {
                      this['blockType'].push('txt1')
                    }
                  }
                  if (this['images'][i] === this['url2']) {
                    this['arrowMovable'][index] = false
                    this['blocksMovable'][i] = false
                    this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1]])
                    this['blocksMovable'].push(true)
                    this.moving = false
                    if (this['arrowType'][index] === 'BIN') {
                      this['images'].push(this['urlb2'])
                      this['blockType'].push('image')
                    }
                    if (this['arrowType'][index] === 'SEG') {
                      this['images'].push(this['urls2'])
                      this['blockType'].push('image')
                    }
                    if (this['arrowType'][index] === 'OCR') {
                      this['blockType'].push('txt2')
                    }
                  }
                  if (this['images'][i] === this['url3']) {
                    this['arrowMovable'][index] = false
                    this['blocksMovable'][i] = false
                    this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1]])
                    this['blocksMovable'].push(true)
                    this.moving = false
                    if (this['arrowType'][index] === 'BIN') {
                      this['images'].push(this['urlb3'])
                      this['blockType'].push('image')
                    }
                    if (this['arrowType'][index] === 'SEG') {
                      this['images'].push(this['urls3'])
                      this['blockType'].push('image')
                    }
                    if (this['arrowType'][index] === 'OCR') {
                      this['blockType'].push('txt3')
                    }
                  }
                  break
                } else {
                  if (this['arrowType'][index] === 'BIN') {
                    this['color'][index] = 'purple'
                  }
                  if (this['arrowType'][index] === 'SEG') {
                    this['color'][index] = 'orange'
                  }
                  if (this['arrowType'][index] === 'OCR') {
                    this['color'][index] = 'turquoise'
                  }
                }
              }
            }
          }
        }
      }
    },

    moveEnd: function (event, type, index) {
      this.moving = false
    },

    addIMG: function (i) {
      this['positionsBlock'].push([360, 150])
      this['blocksMovable'].push(true)
      this['blockType'].push('image')
      if (i === 1) {
        this['images'].push(this['url1'])
      }
      if (i === 2) {
        this['images'].push(this['url2'])
      }
      if (i === 3) {
        this['images'].push(this['url3'])
      }
    },

    addOwnIMG: function (url) {
      this['positionsBlock'].push([400, 200])
      this['blocksMovable'].push(true)
      this['images'].push(url)
      this['blockType'].push('image')
    }
  }
}
</script>
