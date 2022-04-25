<template>
  <div class="room">
    <svg
    xmlns="http://www.w3.org/2000/svg"
    viewBox="0 0 1440 720"
    width="1440"
    height="720"
    aria-labelledby="scissors"
    role="presentation"
  >
    <path
      fill="#ffffff"
      stroke="#b8d6ea"
      stroke-width="2"
      :d="outerWallLines" />
    <path
      v-for="(innerWall, index) in innerWalls"
      :key="index"
      stroke="#b8d6ea"
      stroke-width="2"
      :d="innerWall.line" />
  </svg>
  </div>
</template>

<script>
import Walls from '../json/Walls.json'

export default {
  name: 'RoomLayout',
  computed: {
    outerWallLines() {
    let outerWalls = ""
    for (var i = 0; i < Walls.outerWalls.length; i++) {
      if (i == 0) {
        outerWalls += "M" + Walls.outerWalls[i].x + "," + Walls.outerWalls[i].y + " "
      } else {
        outerWalls += "L" + Walls.outerWalls[i].x + "," + Walls.outerWalls[i].y + " "
      }
    }
    outerWalls += "Z"
      return outerWalls
    },
    innerWalls() {
      for (var i = 0; i < Walls.innerWalls.length; i++) {
        Walls.innerWalls[i].line = "M" + Walls.innerWalls[i].startX + "," + Walls.innerWalls[i].startY + " L" + Walls.innerWalls[i].endX + "," + Walls.innerWalls[i].endY
      }
      return Walls.innerWalls
    }
  },
}
</script>
