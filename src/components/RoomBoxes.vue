<template>
  <div class="boxes">
    <BoxElement
      v-for="boxData in boxesData"
      :key="boxData.id"
      :boxData="boxData"
    />
  </div>
</template>

<script>
import '../css/boxes.css';
import BoxElement from './BoxElement'
import BoxesJson from '../json/BoxesJson.json'

export default {
  name: 'RoomBoxes',
  components: {
    BoxElement
  },
  computed: {
    boxesData() {
      return BoxesJson.data
    }
  },
}
</script>
