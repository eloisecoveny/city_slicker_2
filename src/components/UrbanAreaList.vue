<template lang="html">
  <div id="flex-container">
    <select v-on:change="handleChange" v-model="selectedUrbanAreaIndex">
      <option disabled value="">Select a city</option>
      <option v-for="(urbanArea, index) in urbanAreas" :value="index">{{ urbanArea.name }}</option>
    </select>
  </div>
</template>

<script>
import {eventBus} from '../main.js';

export default {
  name: 'urban-areas-list',
  props: ['urbanAreas', 'selectedCategory'],
  data() {
    return {
      selectedUrbanAreaIndex: ""
    }
  },
  watch: {
    selectedCategory: function(category){
      if(category !== ""){
        this.selectedUrbanAreaIndex = ""
      }
    }
  },
  methods: {
    handleChange() {
      eventBus.$emit('selected-urban-area', this.urbanAreas[this.selectedUrbanAreaIndex]);
    }
  }
}
</script>

<style lang="css" scoped>

#flex-container {
  display: flex;
  justify-content: center;
}

select {
  margin: 30px;
  background-color: White;
  color: black;
  cursor: pointer;
}

select:active, select:hover {
  outline-color: none;
}

@media all and (max-width: 700px){
  select {
    margin-top: 20px;
    margin-bottom: 10px;
  }
}

</style>
