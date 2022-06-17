
<template>
  <div class='container'>
    <h1 class="header">USA States</h1>
    <div class="searchBox">
      <input class="searchField" type='text' autocomplete='off' name='state' v-model='state' placeholder="Enter a state" @input='filterStates' />
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
        filteredStates: [],
        location: { lat: 38.500000, lng: -98 },
      }
    },
    
    methods: {
      filterStates() {
        if (this.state.length > 0) {
          fetch(`https://usa-state-map.herokuapp.com/filterState/${this.state}`)
          .then(resp => resp.json())
          .then(stateArr => {
            let states = []
            stateArr.forEach(state => states.push(state.state))
            this.filteredStates = states
          })
          .catch(console.log)
        } else {
          this.filteredStates = []
        }        
      },
      updateState(filteredState) {
        this.state = filteredState
        this.filteredStates = []
      },
      onClick() {
        fetch(`https://public.opendatasoft.com/api/records/1.0/search/?dataset=us-state-boundaries&q=&facet=name&refine.name=${this.state.charAt(0).toUpperCase()+this.state.slice(1)}`)
        .then(resp => resp.json())
        .then(data => {
          if (data.nhits === 0) {
            alert("State is not found")
          } else {
            let borderArray = data.records[0].fields.st_asgeojson.coordinates
             if (borderArray.length === 1) {
                this.location = { lat: borderArray[0][0][1], lng: borderArray[0][0][0] }
             } else {
                this.location = {lat: borderArray[0][0][0][1], lng: borderArray[0][0][0][0]}
             }
              this.path = this.convertToObjMultipleArray(borderArray)
              this.initMap()
              this.setMarker()
              this.state = ''  
           }         
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
          paths: this.path,
          map: this.map,
          strokeColor: '#FF0000',
          strokeOpacity: 0.8,
          strokeWeight: 3,
          fillColor: '#FF0000',
          fillOpacity: 0.35
        })

      },
      convertToObjArray(arr) {
        let i = 0
        const result = []
        while (i < arr.length) {
          let obj = {}
          obj['lat'] = arr[i][1]
          obj['lng'] = arr[i][0]         
          i++
          result.push(obj)
        }
        return result
      },
      convertToObjMultipleArray(arr){
        const result = []
        for (let i=0; i<arr.length; i++) {
          let temp = this.convertToObjArray(arr[i][0])
          result.push(temp)
        }
        return result
      },
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




