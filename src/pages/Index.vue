<template>
  <q-page>
  <div>
    <toolbar @addlane="incrementLanes()" @addBIN="addArrow('BIN')" @addIMGSEG="addArrow('SEG')" @addOCR="addArrow('OCR')" @image1="addIMG(1)" @image2="addIMG(2)" @image3="addIMG(3)"
    @ownIMG="addOwnIMG"  @undoAction="undo()" @redoAction="redo()"></toolbar>
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
      urlbs1: 'statics/img1binseg.jpg',
      urlbs2: 'statics/img2binseg.jpg',
      urlbs3: 'statics/img3binseg.jpg',
      images: [],
      color: [],
      lanecounter: 1,
      undoStack: [],
      redoStack: []
    }
  },
  methods: {
    addArrow: function (type) {
      this['undoStack'].push('arr')
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
      this['undoStack'].push('ln')
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
                  // Cases for Image being img1 standard (Binarization, Segmentation, OCR)
                  if (this['blockType'][i] === 'image' && this['images'][i] === this['url1']) {
                    this['undoStack'].push('blk')
                    this['arrowMovable'][index] = false
                    this['blocksMovable'][i] = false
                    this['blocksMovable'].push(true)
                    this.moving = false
                    if (this['arrowType'][index] === 'BIN') {
                      this['images'].push(this['urlb1'])
                      this['blockType'].push('image')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1]])
                    }
                    if (this['arrowType'][index] === 'SEG') {
                      this['images'].push(this['urls1'])
                      this['blockType'].push('image')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1]])
                    }
                    if (this['arrowType'][index] === 'OCR') {
                      this['blockType'].push('txt1')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1] + 50])
                      this['images'].push(this['txt'])
                    }
                  }
                  // Cases for Image being img2 standard (Binarization, Segmentation, OCR)
                  if (this['blockType'][i] === 'image' && this['images'][i] === this['url2']) {
                    this['undoStack'].push('blk')
                    this['arrowMovable'][index] = false
                    this['blocksMovable'][i] = false
                    this['blocksMovable'].push(true)
                    this.moving = false
                    if (this['arrowType'][index] === 'BIN') {
                      this['images'].push(this['urlb2'])
                      this['blockType'].push('image')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1]])
                    }
                    if (this['arrowType'][index] === 'SEG') {
                      this['images'].push(this['urls2'])
                      this['blockType'].push('image')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1]])
                    }
                    if (this['arrowType'][index] === 'OCR') {
                      this['blockType'].push('txt2')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1] + 20])
                      this['images'].push(this['txt'])
                    }
                  }
                  // Cases for Image being img3 standard (Binarization, Segmentation, OCR)
                  if (this['blockType'][i] === 'image' && this['images'][i] === this['url3']) {
                    this['undoStack'].push('blk')
                    this['arrowMovable'][index] = false
                    this['blocksMovable'][i] = false
                    this['blocksMovable'].push(true)
                    this.moving = false
                    if (this['arrowType'][index] === 'BIN') {
                      this['images'].push(this['urlb3'])
                      this['blockType'].push('image')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1]])
                    }
                    if (this['arrowType'][index] === 'SEG') {
                      this['images'].push(this['urls3'])
                      this['blockType'].push('image')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1]])
                    }
                    if (this['arrowType'][index] === 'OCR') {
                      this['blockType'].push('txt3')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1] + 20])
                      this['images'].push(this['txt'])
                    }
                  }
                  // Cases for Image being img1 binarized (Segmentation, OCR)
                  if (this['blockType'][i] === 'image' && this['images'][i] === this['urlb1'] &&
                  (this['arrowType'][index] === 'SEG' || this['arrowType'][index] === 'OCR')) {
                    this['undoStack'].push('blk')
                    this['arrowMovable'][index] = false
                    this['blocksMovable'][i] = false
                    this['blocksMovable'].push(true)
                    if (this['arrowType'][index] === 'SEG') {
                      this['images'].push(this['urlbs1'])
                      this['blockType'].push('image')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1]])
                    }
                    if (this['arrowType'][index] === 'OCR') {
                      this['blockType'].push('txt1')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1] + 50])
                      this['images'].push(this['txt'])
                    }
                  }
                  // Cases for Image being img2 binarized (Segmentation, OCR)
                  if (this['blockType'][i] === 'image' && this['images'][i] === this['urlb2'] &&
                  (this['arrowType'][index] === 'SEG' || this['arrowType'][index] === 'OCR')) {
                    this['undoStack'].push('blk')
                    this['arrowMovable'][index] = false
                    this['blocksMovable'][i] = false
                    this['blocksMovable'].push(true)
                    if (this['arrowType'][index] === 'SEG') {
                      this['images'].push(this['urlbs2'])
                      this['blockType'].push('image')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1]])
                    }
                    if (this['arrowType'][index] === 'OCR') {
                      this['blockType'].push('txt2')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1] + 20])
                      this['images'].push(this['txt'])
                    }
                  }
                  // Cases for Image being img3 binarized (Segmentation, OCR)
                  if (this['blockType'][i] === 'image' && this['images'][i] === this['urlb3'] &&
                  (this['arrowType'][index] === 'SEG' || this['arrowType'][index] === 'OCR')) {
                    this['undoStack'].push('blk')
                    this['arrowMovable'][index] = false
                    this['blocksMovable'][i] = false
                    this['blocksMovable'].push(true)
                    if (this['arrowType'][index] === 'SEG') {
                      this['images'].push(this['urlbs3'])
                      this['blockType'].push('image')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1]])
                    }
                    if (this['arrowType'][index] === 'OCR') {
                      this['blockType'].push('txt3')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1] + 20])
                      this['images'].push(this['txt'])
                    }
                  }
                  // Cases for Image being img1 segmented (Binarization, OCR)
                  if (this['blockType'][i] === 'image' && this['images'][i] === this['urls1'] &&
                  (this['arrowType'][index] === 'BIN' || this['arrowType'][index] === 'OCR')) {
                    this['undoStack'].push('blk')
                    this['arrowMovable'][index] = false
                    this['blocksMovable'][i] = false
                    this['blocksMovable'].push(true)
                    if (this['arrowType'][index] === 'BIN') {
                      this['images'].push(this['urlbs1'])
                      this['blockType'].push('image')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1]])
                    }
                    if (this['arrowType'][index] === 'OCR') {
                      this['blockType'].push('txt1')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1] + 50])
                      this['images'].push(this['txt'])
                    }
                  }
                  // Cases for Image being img2 segmented (Binarization, OCR)
                  if (this['blockType'][i] === 'image' && this['images'][i] === this['urls2'] &&
                  (this['arrowType'][index] === 'BIN' || this['arrowType'][index] === 'OCR')) {
                    this['undoStack'].push('blk')
                    this['arrowMovable'][index] = false
                    this['blocksMovable'][i] = false
                    this['blocksMovable'].push(true)
                    if (this['arrowType'][index] === 'BIN') {
                      this['images'].push(this['urlbs2'])
                      this['blockType'].push('image')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1]])
                    }
                    if (this['arrowType'][index] === 'OCR') {
                      this['blockType'].push('txt2')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1] + 20])
                      this['images'].push(this['txt'])
                    }
                  }
                  // Cases for Image being img3 segmented (Binarization, OCR)
                  if (this['blockType'][i] === 'image' && this['images'][i] === this['urls3'] &&
                  (this['arrowType'][index] === 'BIN' || this['arrowType'][index] === 'OCR')) {
                    this['undoStack'].push('blk')
                    this['arrowMovable'][index] = false
                    this['blocksMovable'][i] = false
                    this['blocksMovable'].push(true)
                    if (this['arrowType'][index] === 'BIN') {
                      this['images'].push(this['urlbs3'])
                      this['blockType'].push('image')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1]])
                    }
                    if (this['arrowType'][index] === 'OCR') {
                      this['blockType'].push('txt3')
                      this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1] + 20])
                      this['images'].push(this['txt'])
                    }
                  }
                  // Cases for Image being img1 segmented and binarized (OCR)
                  if (this['blockType'][i] === 'image' && this['images'][i] === this['urlbs1'] && this['arrowType'][index] === 'OCR') {
                    this['undoStack'].push('blk')
                    this['arrowMovable'][index] = false
                    this['blocksMovable'][i] = false
                    this['blocksMovable'].push(true)
                    this['blockType'].push('txt1')
                    this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1] + 50])
                    this['images'].push(this['txt'])
                  }
                  // Cases for Image being img2 segmented and binarized (OCR)
                  if (this['blockType'][i] === 'image' && this['images'][i] === this['urlbs2'] && this['arrowType'][index] === 'OCR') {
                    this['undoStack'].push('blk')
                    this['arrowMovable'][index] = false
                    this['blocksMovable'][i] = false
                    this['blocksMovable'].push(true)
                    this['blockType'].push('txt2')
                    this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1] + 20])
                    this['images'].push(this['txt'])
                  }
                  // Cases for Image being img3 segmented and binarized (OCR)
                  if (this['blockType'][i] === 'image' && this['images'][i] === this['urlbs3'] && this['arrowType'][index] === 'OCR') {
                    this['undoStack'].push('blk')
                    this['arrowMovable'][index] = false
                    this['blocksMovable'][i] = false
                    this['blocksMovable'].push(true)
                    this['blockType'].push('txt3')
                    this['positionsBlock'].push([this['positionsBlock'][i][0] + 290, this['positionsBlock'][i][1] + 20])
                    this['images'].push(this['txt'])
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
      this['undoStack'].push('blk')
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
      this['undoStack'].push('blk')
      this['positionsBlock'].push([400, 200])
      this['blocksMovable'].push(true)
      this['images'].push(url)
      this['blockType'].push('image')
    },

    undo: function () {
      if (this['undoStack'][this['undoStack'].length - 1] === 'arr') {
        this['redoStack'].push(['arr', this['positionsArrow'][this['positionsArrow'].length - 1],
          this['arrowMovable'][this['positionsArrow'].length - 1], this['arrowType'][this['positionsArrow'].length - 1],
          this['color'][this['positionsArrow'].length - 1]])
        this['arrowMovable'].splice(this['positionsArrow'].length - 1, 1)
        this['arrowType'].splice(this['positionsArrow'].length - 1, 1)
        this['color'].splice(this['positionsArrow'].length - 1, 1)
        this['positionsArrow'].splice(this['positionsArrow'].length - 1, 1)
      }
      if (this['undoStack'][this['undoStack'].length - 1] === 'ln') {
        this['lanecounter'] -= 1
        this['redoStack'].push('ln')
      }
      if (this['undoStack'][this['undoStack'].length - 1] === 'blk') {
        this['redoStack'].push(['blk', this['positionsBlock'][this['positionsBlock'].length - 1],
          this['blocksMovable'][this['positionsBlock'].length - 1], this['blockType'][this['positionsBlock'].length - 1],
          this['images'][this['positionsBlock'].length - 1]])
        this['blocksMovable'].splice(this['positionsBlock'].length - 1, 1)
        this['blockType'].splice(this['positionsBlock'].length - 1, 1)
        this['images'].splice(this['positionsBlock'].length - 1, 1)
        this['positionsBlock'].splice(this['positionsBlock'].length - 1, 1)
      }
      this['undoStack'].splice(this['undoStack'].length - 1, 1)
    },

    redo: function () {
      if (this['redoStack'][this['redoStack'].length - 1] === 'ln') {
        this.incrementLanes()
      }
      if (this['redoStack'][this['redoStack'].length - 1][0] === 'arr') {
        this['positionsArrow'].push(this['redoStack'][this['redoStack'].length - 1][1])
        this['arrowMovable'].push(this['redoStack'][this['redoStack'].length - 1][2])
        this['arrowType'].push(this['redoStack'][this['redoStack'].length - 1][3])
        this['color'].push(this['redoStack'][this['redoStack'].length - 1][4])
        this['undoStack'].push('arr')
      }
      if (this['redoStack'][this['redoStack'].length - 1][0] === 'blk') {
        this['positionsBlock'].push(this['redoStack'][this['redoStack'].length - 1][1])
        this['blocksMovable'].push(this['redoStack'][this['redoStack'].length - 1][2])
        this['blockType'].push(this['redoStack'][this['redoStack'].length - 1][3])
        this['images'].push(this['redoStack'][this['redoStack'].length - 1][4])
        this['undoStack'].push('blk')
      }
      this['redoStack'].splice(this['redoStack'].length - 1, 1)
    }
  }
}
</script>
