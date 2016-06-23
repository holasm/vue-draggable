<template>
<div class="container"
  @mouseup='mouseup'
  @mousemove='mousemove'>
  <slot></slot>
  <draggable v-for="draggable in draggables" :data.sync='draggable' :container='container' :drag-start='dragStart' :on-drag='dragging' :drag-end='dragEnd'>
    <div slot='dragger' class='dragger'>
      <div class="head">
        <h2>Title</h2>
      </div>
    </div>
    <div slot='content' class="content">
      <h2>{{draggable.content}}</h2>
    </div>
  </draggable>
</template>
</div>

<script>
import Draggable from './../components/Draggable'

export default {
  components: {
    Draggable
  },
  props: {
    adjustable: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      container: {},
      x: 0,
      y: 0,
      h: 0,
      w: 0,
      draggables: [
        {
          x: 20,
          y: 100,
          w: 200,
          h: 200,
          stepX: 10,
          stepY: 10,
          content: 'In step of 10'
        },
        {
          x: 240,
          y: 100,
          w: 200,
          h: 200,
          stepX: 20,
          stepY: 20,
          content: 'In step of 20'
        },
        {
          x: 460,
          y: 100,
          w: 200,
          h: 200,
          content: 'Smooth drag'
        },
        {
          x: 680,
          y: 100,
          w: 200,
          h: 200,
          content: 'Smooth drag'
        },
        {
          x: 20,
          y: 300,
          w: 200,
          h: 200,
          content: 'Smooth drag'
        },
        {
          x: 240,
          y: 300,
          w: 200,
          h: 200,
          content: 'Smooth drag'
        },
        {
          x: 460,
          y: 300,
          w: 200,
          h: 200,
          content: 'Smooth drag'
        },
        {
          x: 680,
          y: 300,
          w: 200,
          h: 200,
          content: 'Smooth drag'
        }
      ],
      el_ds: {},
      nx: null,
      ny: null
    }
  },
  methods: {
    mouseup (e) {
      if (this.container.dragActiveEl) {
        (this.container.dragActiveEl.state === 'moving' || this.container.dragActiveEl.state === 'move_active') ? this.container.dragActiveEl.state = 'rest' : ''
      }
    },
    mousemove (e) {
      // for pure draggable components
      if (this.container.dragActiveEl) {
        switch (this.container.dragActiveEl.state) {
          case 'moving':
            this.container.dragActiveEl.move(e)
            break
        }
      }
    },
    dragStart (e, el) {
      if (this.adjustable) {
        this.el_ds.draggable.forEach(function (el, index) {
          el.capturePosState(e)
        })
        this.nx = el.lastX
        this.ny = el.lastY
      }
    },
    dragging (e, dragData, dragEl) {
      // make swappaable items
      // only equal size and evenly placed elements are swappable
      if (this.adjustable) {
        var self = this
        this.el_ds.draggable.forEach(function (el, index) {
          var gotoX, gotoY
          if (el !== dragEl) {
            // horizontal swapping
            if (dragEl.data.x >= self.nx) {
              if (dragEl.data.x + dragEl.data.w > el.data.x + el.data.w / 2 && self.nx < el.lastX) {
                if (self.ny === el.lastY) {
                // if (dragEl.data.y + dragEl.data.h / 2 >= el.data.y && dragEl.data.y + dragEl.data.h / 2 <= el.data.y + el.data.h) {
                  gotoX = self.nx
                  self.nx = el.data.x
                  el.data.x = gotoX
                  el.lastX = gotoX
                }
                // }
              }
            } else if (dragEl.data.x < self.nx) {
              if (dragEl.data.x < el.data.x + el.data.w / 2 && self.nx > el.lastX) { //
                if (self.ny === el.lastY) {
                // if (dragEl.data.y + dragEl.data.h / 2 >= el.data.y && dragEl.data.y + dragEl.data.h / 2 <= el.data.y + el.data.h) {
                  gotoX = self.nx
                  self.nx = el.data.x
                  el.data.x = gotoX
                  el.lastX = gotoX
                }
                // }
              }
            }

            // vertical swapping
            if (dragEl.data.y >= self.ny) {
              if (dragEl.data.y + dragEl.data.h > el.data.y + el.data.h / 2 && self.ny < el.lastY) {
                if (self.nx === el.lastX) {
                // if (dragEl.data.x + dragEl.data.w / 2 >= el.data.x && dragEl.data.x + dragEl.data.w / 2 <= el.data.x + el.data.h) {
                  gotoY = self.ny
                  self.ny = el.data.y
                  el.data.y = gotoY
                  el.lastY = gotoY
                }
                // }
              }
            } else if (dragEl.data.y < self.ny) { // moving down
              if (dragEl.data.y < el.data.y + el.data.h / 2 && self.ny > el.lastY) { //
                if (self.nx === el.lastX) {
                // if (dragEl.data.x + dragEl.data.w / 2 >= el.data.x && dragEl.data.x + dragEl.data.w / 2 <= el.data.x + el.data.h) {
                  gotoY = self.ny
                  self.ny = el.data.y
                  el.data.y = gotoY
                  el.lastY = gotoY
                }
              }
            }
          }
        })
      }
    },
    dragEnd (e, dragEl, skip) {
      if (this.adjustable) {
        // console.log(this.nx)
        if (this.nx !== null) {
          dragEl.data.x = this.nx
          this.nx = null
        }
        if (this.ny !== null) {
          dragEl.data.y = this.ny
          this.ny = null
        }
      }
    }
  },
  ready () {}
}
</script>

<style lang='scss'>
html, body{
  margin: 0;
  padding: 0;
}
html {
  height: 100%;
}
body {
  /* display: flex; */
  align-items: center;
  justify-content: center;
  height: 100%;
}
.container{
  /* left: 0; */
  /* position: absolute; */
  position: relative;
  top: 0;
  width: 1300px;
  height: 500px;
  background: #e3e3e3;
  overflow: hidden;
  left: 50%;
  transform: translateX(-50%);
  margin-bottom: 10px;
}
.dragger{
  /* width: 200px; */
  width: 100%;
  height: 100%;
  /* background: #aaa; */
  display: block;
  cursor: move;

  .head{
    text-align: center;
    height: 50px;
    width: 100%;
    background: teal;
    display: block;
    color: white;
    h2{
      margin: 0;
      padding: 10px 8px;
    }
  }
}
.content{
  box-sizing: border-box;
  position: absolute;
  width: 100%;
  /* height: calc(100px); */
  /* max-height: 140px; */
  background: white;
  top: 50px;
  /* left: 10px; */
  overflow: hidden;
  padding: 10px;
}

</style>
