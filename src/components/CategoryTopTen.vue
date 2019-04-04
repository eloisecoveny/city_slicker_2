<template lang="html">
  <div id="flex-container" v-if="topTen">
    <h2>Top Ten Cities</h2>
    <p>{{ selectedCategory }}</p>
    <div id="top-ten-display">

      <div id="names">
        <h4 class="heading">City</h4>
        <p v-for="city in topTen" v-on:click="selectCity">{{ city.name }}</p>
      </div>

      <div id="scores">
        <h4 class="heading">Score out of 10</h4>
        <p v-for="city in topTen">{{ city.score }}</p>
      </div>

    </div>
  </div>

</template>

<script>
import {eventBus} from '../main.js';

export default {
  name: 'category-top-ten',
  props: ['topTen', 'selectedCategory','urbanAreas'],
  methods: {
    selectCity(event){
      eventBus.$emit('city-selected', (event.target.textContent));
    }
  }
}
</script>

<style lang="css" scoped>

h2 {
  font-family: 'Nova Round', cursive;
  padding: 5px;
  margin-top: 70px;
  margin-bottom: 0px;
  font-size: 35px;
}

h4 {
  font-family: 'Nanum Gothic', sans-serif;
  padding: 5px;
  margin-bottom: 15px;
}

p {
  font-family: 'Nanum Gothic', sans-serif;
  padding: 5px;
  margin: 5px;
}

#top-ten-display {
  display: flex;
  flex-direction: row;
}

#top-ten-display p {
  font-size: 15px;
}

.heading {
  font-weight: bold;
  color: rgb(247, 185, 148);
}

#names {
  display: flex;
  flex-direction: column;
  cursor: pointer;
}

#scores {
  display: flex;
  flex-direction: column;
  margin-left: 100px;
}

</style>
