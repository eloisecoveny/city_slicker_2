<template lang="html">
  <div id="flex-container">
    <select v-on:change="handleChange" v-model="selectedCategoryIndex">
      <option disabled value="">Select a category</option>
      <option v-for="(category, index) in categories" :value="index">{{ category }}</option>
    </select>
  </div>
</template>

<script>
import {eventBus} from '../main.js';

export default {
  name: 'category-list',
  props: ['categories', 'selectedUrbanArea'],
  data() {
    return {
      selectedCategoryIndex: ""
    }
  },
  watch: {
    selectedUrbanArea: function(urbanArea){
      if(urbanArea !== ""){
        this.selectedCategoryIndex = ""
      }
    }
  },
  methods: {
    handleChange() {
      eventBus.$emit('selected-category', this.selectedCategoryIndex);
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

</style>
