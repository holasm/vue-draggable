<template>
<div id="draggable" v-bind:style='style' v-bind:class='classNames'>
  <div class="dragger-slot" @mousedown='_dragStart' @mouseup='_dragEnd' @mousemove='_onDrag'>
    <slot name='dragger'></slot>
    <slot></slot>
  </div>
    <slot name='content'></slot>
</div>
</template>

<script>
// import app from './../globals'

function noop () {}
var data1 = {
  x: 0,
  y: 0,
  w: 0,
  h: 0,
  stepX: 1,
  stepY: 1
}
// not boundable
export default {
  props: {
    data: {
      type: Object,
      default: data1
    }, // {x,y}
    // x: Number,
    // y: Number,
    dragStart: {
      type: Function,
      default: noop
    },
    dragEnd: {
      type: Function,
      default: noop
    },
    onDrag: {
      type: Function,
      default: noop
    },
    helpParent: Boolean,
    dontMove: Boolean,
    container: Object
  },
  data () {
    this.data.stepX = this.data.stepX || 1
    this.data.stepY = this.data.stepY || 1
    return {
      lastX: this.data.x,
      lastY: this.data.y,
      deltaX: 0,
      deltaY: 0,
      initX: 0,
      initY: 0,
      state: 'rest',
      movable: true,
      zi: 0
    }
  },
  computed: {
    style () {
      // this.x += this.deltaX
      // this.deltaX = 0
      // this.y += this.deltaY
      // this.deltaY = 0

      return {
        left: this.data.x + 'px',
        top: this.data.y + 'px',
        width: this.data.w + 'px',
        height: this.data.h + 'px',
        'z-index': this.zi
      }
    },
    classNames () {
      return {
        'move-active': this.state === 'move_active',
        'rest': this.state === 'rest',
        'moving': this.state === 'moving'
      }
    }
  },
  watch: {
    state: function (newVal, oldVal) {
      if (newVal === 'rest' && oldVal === 'moving') {
        this.dragEnd(null, this, true)
      }
    }
  },
  methods: {
    _dragStart (e) {
      if (!this.dontMove) {
        this.zi = 100
        e.stopPropagation()
        e.preventDefault()
        this.state = 'move_active'
        this.capturePosState(e)
        this.container.dragActiveEl = this
        this.dragStart(e, this)
      }
    },
    _dragEnd (e) {
      e.stopPropagation()
      if (!this.dontMove) {
        this.zi = 0
        e.preventDefault()
        if (this.container.dragActiveEl === this) {
          this.container.dragActiveEl.state = 'rest'
        }
        this.dragEnd(e, this)
      }
    },
    _onDrag (e) {
      if (!this.dontMove) {
        e.stopPropagation()
        e.preventDefault()
        if (this.state === 'move_active' || this.state === 'moving') {
          this.move(e)
        }
      }
    },
    move (e) {
      this.state = 'moving'
      if (this.state === 'moving') {
        var deltaX = e.clientX - this.initX
        var deltaY = e.clientY - this.initY
        if (this.helpParent) {
          this.onDrag(e, {dx: deltaX, dy: deltaY})
        } else {
          var x = this.lastX + deltaX
          var y = this.lastY + deltaY
          // update the store x, y here
          this.data.x = this.rountBy(x, this.data.stepX)
          this.data.y = this.rountBy(y, this.data.stepY)
          this.onDrag(e, {x, y}, this)
        }
      }
    },
    rountBy (val, step) {
      var temp = val % step
      if (temp >= step / 2) {
        return val - temp + step
      } else {
        return val - temp
      }
    },
    capturePosState (e) {
      this.lastX = this.data.x
      this.lastY = this.data.y
      this.initX = e.clientX
      this.initY = e.clientY
      this.initH = this.h
      this.initW = this.w
    }
  },
  ready () {
    // this.$parent.$parent.el_ds.draggable = this
    this.$parent.el_ds.draggable = this.$parent.el_ds.draggable || []
    this.$parent.el_ds.draggable.push(this)
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  #draggable{
    position: absolute;
    box-sizing: border-box;
    cursor: default;
    overflow: hidden;
    /* background: teal; */
  }
.dragger-slot{
  position: absolute;
  overflow: hidden;
  height: 100%;
  width: 100%;
}
/*   .rest{
    border: 1px solid teal;
    background: white;
  }
  
  .moving,
  .move-active
  {
    cursor: default; 
    background: teal;
    border: 1px solid teal;
  } */
</style>
