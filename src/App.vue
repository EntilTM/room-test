<template>
  <RoomLayout />
  <RoomBoxes />
</template>

<script>
import RoomLayout from './components/RoomLayout.vue'
import RoomBoxes from './components/RoomBoxes.vue'
import './css/reset.css';

export default {
  name: 'App',
  components: {
    RoomLayout,
    RoomBoxes
  }
}
</script>