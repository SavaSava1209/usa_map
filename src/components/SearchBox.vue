
<template>
  <div class="searchBox">
    <input class="searchField"  type='text' name='state' v-model='state' placeholder="enter a state" />
    <button type='button' @click="onClick()">submit</button>
  </div>
    
</template>

<script >
  export default {
    name:'SearchBox',
    data() {
      return {
        state: ''
      }
    },
    methods: {
      onClick() {
        console.log(this.state)
        fetch(`http://localhost:3010/${this.state}`)
        .then(resp => resp.json())
        .then(data => console.log(data))
        .catch(console.log)
      }
    }
  }
</script>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  top: -10px;
}


.searchBox {
  margin: 10px;
}
input {
  padding:  5px;
}
button {
  margin: 0 0 0 0;
  padding: 5px;
}
</style>
