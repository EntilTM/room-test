<template>
  <div ref="draggableContainer"
    class="box"
    :style="'top: '+ currentY + 'px;left: '+ currentX + 'px'"
    @mousedown="dragMouseDown"
  >
    {{boxData.name}}
  </div>
</template>

<script>
import Walls from '../json/Walls.json'

export default {
  name: 'BoxElement',
  props: {
    boxData: Object,
    boxIndex: Number,
  },
  data: function () {
    return {
      positions: {
        startX: undefined,
        startY: undefined,
        endX: undefined,
        endY: undefined,
        clientX: undefined,
        clientY: undefined,
        movementX: 50,
        movementY: 0
      },
      sortedWalls: [...Walls.outerWalls].sort((a, b) => {
        return a.x - b.x
      })
    }
  },
  methods: {
    dragMouseDown: function (event) {
      event.preventDefault()
      this.positions.startX = this.$refs.draggableContainer.style.left
      this.positions.startY = this.$refs.draggableContainer.style.top
      this.positions.clientX = event.clientX
      this.positions.clientY = event.clientY
      document.onmousemove = this.elementDrag
      document.onmouseup = this.closeDragElement
    },
    elementDrag: function (event) {
      event.preventDefault()
      this.positions.movementX = this.positions.clientX - event.clientX
      this.positions.movementY = this.positions.clientY - event.clientY
      this.positions.clientX = event.clientX
      this.positions.clientY = event.clientY
      this.positions.endX = this.$refs.draggableContainer.offsetLeft - this.positions.movementX
      this.positions.endY = this.$refs.draggableContainer.offsetTop - this.positions.movementY
      this.$refs.draggableContainer.style.left = (this.$refs.draggableContainer.offsetLeft - this.positions.movementX) + 'px'
      this.$refs.draggableContainer.style.top = (this.$refs.draggableContainer.offsetTop - this.positions.movementY) + 'px'
    },
    closeDragElement () {
      const didCollideInner = this.checkCollideInner(this.positions.endX, this.positions.endY);
      const didCollideOuter = this.checkCollideOuter(this.positions.endX, this.positions.endY);
      if (didCollideInner || didCollideOuter) {
        this.$refs.draggableContainer.style.left = this.positions.startX
        this.$refs.draggableContainer.style.top = this.positions.startY
      }
      localStorage.setItem(this.$props.boxData.id, '{"x": '+ this.positions.endX +', "y": ' + this.positions.endY + '}');
      document.onmouseup = null
      document.onmousemove = null
    },
    startData () {
      let boxStorageData = localStorage.getItem(this.$props.boxData.id);
      if (boxStorageData) {
        try {
          boxStorageData = JSON.parse(boxStorageData)
          if (boxStorageData?.x) {
            this.positions.storedX = boxStorageData.x
          }
          if (boxStorageData?.y) {
            this.positions.storedY = boxStorageData.y
          }
        } catch (e) {
          console.log(e)
        }
      }
    },
    checkCollideInner (endX , endY) {
      let didCollide = false
      for (var i = 0; i < Walls.innerWalls.length; i++) {
        if (
          ((endX <= Walls.innerWalls[i].startX && (endX + 195) >= Walls.innerWalls[i].startX) ||
          (endX <= Walls.innerWalls[i].endX && (endX + 195) >= Walls.innerWalls[i].endX) ||
          (endX >= Walls.innerWalls[i].startX && (endX + 195) <= Walls.innerWalls[i].endX) ||
          (endX >= Walls.innerWalls[i].endX && (endX + 195) <= Walls.innerWalls[i].startX)) &&
          ((endY <= Walls.innerWalls[i].startY && (endY + 90) >= Walls.innerWalls[i].startY) ||
          (endY <= Walls.innerWalls[i].endY && (endY + 90) >= Walls.innerWalls[i].endY) ||
          (endY >= Walls.innerWalls[i].startY && (endY + 90) <= Walls.innerWalls[i].endY) ||
          (endY >= Walls.innerWalls[i].endY && (endY + 90) <= Walls.innerWalls[i].startY))
        ) {
          didCollide = true;
          break;
        }
      }
      return didCollide
    },
    checkCollideOuter (endX , endY) {
      let didCollide = true
      let lastX = 0
      for (var i = this.sortedWalls.length -1; i >= 0; i--) {
        if (this.sortedWalls[i].x <= (endX + 195)) {
          lastX = this.sortedWalls[i+1].x
          break;
        }
      }
      let lastLine = [];
      let minY = 720
      let maxY = 0
      for (let i = 0; i < Walls.outerWalls.length - 1; i++) {
        if (Walls.outerWalls[i].x === lastX) {
          if (Walls.outerWalls[i].y < minY) {
            minY = Walls.outerWalls[i].y
          }
          if (Walls.outerWalls[i].y > maxY) {
            maxY = Walls.outerWalls[i].y
          }
          lastLine.push(Walls.outerWalls[i]);
        }
      }
      if (endY > minY && (endY + 90) < maxY) {
        didCollide = false
      }
      return didCollide
    }
  },
  computed: {
    currentX: function() {
      return this.positions.storedX ? this.positions.storedX : this.$props.boxData.positionX
    },
    currentY: function() {
      return this.positions.storedY ? this.positions.storedY : this.$props.boxData.positionY
    }
  },
  beforeMount(){
    this.startData()
 },
}
</script>