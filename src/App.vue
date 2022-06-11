
<template>
  <div class='container'>
    <h1 class="header">USA States</h1>
    <div class="searchBox">
      <input class="searchField" type='text' autocomplete='off' name='state' v-model='state' placeholder="enter a state" @input='filterStates' />
      <button type='button' @click="onClick()">Find</button>
      <div v-if="filteredStates">
        <ul>
          <li v-for="filteredState in filteredStates" @click="updateState(filteredState)">
           {{ filteredState }}
          </li>
        </ul>
      </div>
      
    </div>
    <div id='map' ref='map'></div>
  </div>
</template>

<script>
  export default {
    name: "App",
    data() {
      return {
        map: null,
        state: '',
        states: ['Alabama', 'Alaska', 'Arizona', 'Arkansas', 'California',  'Colorado', 'Connecticut', 'Delaware', 'Florida', 'Georgia', 'Hawaii',  'Idaho', 'Illinois', 'Indiana', 'Iowa', 'Kansas', 'Kentucky', 'Louisiana',  'Maine', 'Maryland', 'Massachusetts', 'Michigan', 'Minnesota',  'Mississippi', 'Missouri', 'Montana', 'Nebraska', 'Nevada', 'New Hampshire','New Jersey', 'New Mexico', 'New York', 'North Carolina', 'North Dakota',  'Ohio', 'Oklahoma', 'Oregon', 'Pennsylvania', 'Rhode Island',  'South Carolina', 'South Dakota', 'Tennessee', 'Texas','Utah', 'Vermont',  'Virginia', 'Washington', 'West Virginia', 'Wisconsin', 'Wyoming', 'American Samoa',  'Guam','Northern Mariana Islands', 'Puerto Rico', 'Virgin Islands'],
        filteredStates: [],
        location: { lat: 38.500000, lng: -98 },
        
      }
    },
    
    methods: {
      filterStates() {
        if (this.state.length > 0) {
          this.filteredStates = this.states.filter(state => {
          return state.toLowerCase().startsWith(this.state.toLowerCase())
          })
        } else {
          this.filteredStates = []
        }        
      },
      updateState(filteredState) {
        this.state = filteredState
        this.filteredStates = []
      },
      onClick() {
        fetch(`http://localhost:3010/${this.state}`)
        .then(resp => resp.json())
        .then(data => {  
          console.log(data)
          this.location = {lat: Number(data[0].latitude), lng: Number(data[0].longtitude)}
          this.path = this.convertToArray(data[0].state_path)
          console.log(this.path)
          this.initMap()
          this.setMarker()
          this.state = ''
        })
        .catch(console.log)
      },
      initMap() {
        this.map = new window.google.maps.Map(this.$refs['map'], {
          center: this.location,
          zoom: 4
        })
      },
      setMarker() {
        const marker = new window.google.maps.Polygon({
          path: this.path,
          map: this.map,
          strokeColor: '#FF0000',
          strokeOpacity: 0.8,
          strokeWeight: 3,
          fillColor: '#FF0000',
          fillOpacity: 0.35
        })

      },
      convertToArray(arr) {
        const result = []
        let i = 0
        while ( i < arr.length) {
          let obj = {}
          obj['lat'] = arr[i][1]
          obj['lng'] = arr[i][0]
          result.push(obj)
          i++
        }
        return result
      }
    },
    mounted() {
      this.initMap()
    },
  }
</script>

<style>
@import './assets/base.css';

#app {
  max-width: 1280px;
  margin: 0 auto;
  padding: 2rem;
  font-weight: normal;
}

.container {
  line-height: 1.5;
  text-align: center;
}

 #map {
    width: 100%;
    height: 700px;
    border: 2px solid black;
    margin-top: 4rem;
  }

 .searchBox {
  margin: 10px;
}
input {
  width:  60%;
  padding:  5px;
}

button {
  margin: 0 0 0 0;
  padding: 5px;
}
li {
  width: 60%;
  list-style: none;
}
li:hover {
  position: cursor;
  background-color: grey ;
}

</style>




