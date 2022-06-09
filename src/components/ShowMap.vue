<template>
  <div>
    <div id='map' ref='map'>
      
    </div>
    
  </div>
    
</template>

<script a\>
  export default {
    name: "Map",
    data() {
      return {
        map: null,
      }
    },
    mounted() {
      this.map  = new window.google.maps.Map(this.$refs['map'], {
      center: { lat: 36, lng: -119},
      zoom: 5
      })
    }
  }


</script>

<style scoped>
  #map {
    width: 100%;
    height: 500px;
    border: 2px solid black;
  }
</style>