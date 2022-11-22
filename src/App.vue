<template>
  <div
    class="background"
    :class="{ dragging: draggable.isDraggin }"
    @mouseup="end"
    @touchend="end"
    @mousemove="move"
    @touchmove="move"
  >
    <div class="workarea" ref="workarea">
      <div
        class="draggable"
        @mousedown="start"
        @touchstart="start"
        :style="{
          top: draggable.y + 'px',
          left: draggable.x + 'px',
          width: draggable.width + 'px',
          height: draggable.height + 'px',
        }"
      ></div>
    </div>
  </div>
</template>

<script>

export default {
  data () {
    return {
      // Draggable object
      draggable: {
        isDraggin: false,
        y: 0,
        x: 0,
        width: 80,
        height: 80
      },
      // Drag offset point
      dragOffset: {
        x: 0,
        y: 0
      },
      // Drag start position
      dragStart: {
        x: 0,
        y: 0
      },
      // Workarea borders
      borders: {
        top: 0,
        right: 0,
        bottom: 0,
        left: 0
      }
    }

  },
  methods: {
    start (e) {

      this.dragOffset.x = this.eventPoint(e)['x'];
      this.dragOffset.y = this.eventPoint(e)['y'];

      this.dragStart.x = this.draggable.x;
      this.dragStart.y = this.draggable.y;

      this.draggable.isDraggin = true;

    },
    move (e) {
      if (this.draggable.isDraggin) {

        let x = this.dragStart.x + this.eventPoint(e)['x'] - this.dragOffset.x;
        let y = this.dragStart.y + this.eventPoint(e)['y'] - this.dragOffset.y;

        this.draggable.x = x < this.borders.left ? this.borders.left : x <= this.borders.right ? x : this.borders.right;
        this.draggable.y = y < this.borders.top ? this.borders.top : y <= this.borders.bottom ? y : this.borders.bottom;

      }
    },
    end (e) {
      this.draggable.isDraggin = false;
    },
    // Point depends on event (mouse or touch)
    eventPoint (e) {
      return {
        x: e.touches ? e.touches[0].clientX : e.clientX,
        y: e.touches ? e.touches[0].clientY : e.clientY
      }
    },
    defineBorders () {
      this.dragArea = this.$refs.workarea;

      this.borders = {
        top: this.dragArea.getBoundingClientRect().top,
        right: this.dragArea.getBoundingClientRect().left + this.dragArea.clientWidth - this.draggable.width,
        bottom: this.dragArea.getBoundingClientRect().top + this.dragArea.clientHeight - this.draggable.height,
        left: this.dragArea.getBoundingClientRect().left
      }
    },
    setDraggablePosition () {
      // Set draggable start position to center
      this.draggable.x = (window.innerWidth - this.draggable.width) / 2;
      this.draggable.y = (window.innerHeight - this.draggable.height) / 2;
    },
    correctOnResize () {
      this.defineBorders()

      if (this.draggable.x > this.borders.right)
        this.draggable.x = this.borders.right;

      if (this.draggable.x < this.borders.left)
        this.draggable.x = this.borders.left;
    }
  },
  created () {
    window.addEventListener('resize', this.correctOnResize)
  },
  mounted () {
    this.setDraggablePosition()
    this.defineBorders()
  }
}
</script>

<style scoped>
.background {
  background-color: #e0a7fe;
  position: relative;
  width: 100vw;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}
.workarea {
  background-color: #0097ff;
  width: 80vw;
  height: 80vh;
}
.draggable {
  background-color: #ffd028;
  position: absolute;
  cursor: move;
  cursor: grab;
  cursor: -moz-grab;
  cursor: -webkit-grab;
}
.draggable:active,
.dragging {
  cursor: grabbing;
  cursor: -moz-grabbing;
  cursor: -webkit-grabbing;
}
</style>
