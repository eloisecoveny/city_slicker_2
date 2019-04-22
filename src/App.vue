<template lang="html">
  <div id="flex-container">
    <link href="https://fonts.googleapis.com/css?family=Nova+Round|Nanum+Gothic" rel="stylesheet">
    <h1 v-on:click="
      selectedUrbanArea = '',
      selectedCategoryIndex = null,
      selectedCategory = ''">City Slicker</h1>
    <nav>
      <div id="urban-areas-list">
        <urban-areas-list :urbanAreas="urbanAreas" :selectedCategory="selectedCategory"></urban-areas-list>
      </div>

      <div id="category-list">
        <category-list :categories="categories" :selectedUrbanArea="selectedUrbanArea"></category-list>
      </div>
    </nav>

    <div v-if="(!selectedUrbanArea && !selectedCategoryIndex) && !selectedCategory">
      <img src="https://cdnb.artstation.com/p/media_assets/images/images/000/204/827/large/NEWCITIY01-S.jpg?1522210659" alt="cartoon-city-scape">
    </div>

    <div id="urban-area-data">
      <urban-area-data v-if="!selectedCategory" :urbanArea="selectedUrbanArea" :continent="continent" :country="country" :image="image" :scores="scores"></urban-area-data>
    </div>

    <div id="category-top-ten">
      <category-top-ten v-if="selectedCategory" :topTen="categoryTopTen" :selectedCategory="selectedCategory" :urbanAreas="urbanAreas"></category-top-ten>
    </div>

  </div>
</template>

<script>
import UrbanAreaList from './components/UrbanAreaList.vue';
import UrbanAreaData from './components/UrbanAreaData.vue';
import CategoryList from './components/CategoryList.vue';
import CategoryTopTen from './components/CategoryTopTen.vue'
import {eventBus} from './main.js';

export default {
  name: 'app',
  data(){
    return {
      urbanAreas: [],
      selectedUrbanArea: "",
      continent: "",
      country: "",
      image: "",
      scores: [],
      selectedCategory: "",
      selectedCategoryIndex: null,
      categories: ["Housing", "Cost of Living", "Startups", "Venture Capital", "Travel Connectivity", "Commute", "Business Freedom", "Safety", "Healthcare", "Education", "Environmental Quality", "Economy", "Taxation", "Internet Access", "Leisure & Culture", "Tolerance", "Outdoors"],
      categoryTopTen: [],
      categoryScores: []
    }
  },
  components: {
    "urban-areas-list": UrbanAreaList,
    "urban-area-data": UrbanAreaData,
    "category-list": CategoryList,
    "category-top-ten": CategoryTopTen
  },
  mounted(){
    fetch("https://api.teleport.org/api/urban_areas/")
    .then(response => response.json())
    .then(data => this.urbanAreas = data["_links"]["ua:item"])

    eventBus.$on('selected-urban-area', (urbanArea) => {
      this.selectedUrbanArea = urbanArea;
      this.fetchData();
    });

    eventBus.$on('selected-category', (categoryIndex) => {
      this.selectedCategory = this.categories[categoryIndex]
      this.selectedCategoryIndex = categoryIndex;
      this.getCategoryScores(categoryIndex);
      this.selectedUrbanArea = ""
    });

    eventBus.$on('city-selected', (cityName) => {
      let city = this.urbanAreas.filter(city => {
        return city.name === cityName
      })
      this.selectedUrbanArea = city[0];
      this.fetchData();
    })
  },
  methods: {
    fetchData(){
      fetch(this.selectedUrbanArea["href"])
      .then(response => response.json())
      .then(data => {
        this.continent = data.continent;
        this.country = data["_links"]["ua:countries"][0]["name"];
        this.getImage(data);
        this.getScores(data);
        this.selectedCategory = ""
        this.categoryTopTen = []
      });
    },
    getImage(data){
      fetch(data["_links"]["ua:images"]["href"])
      .then(response => response.json())
      .then(images => this.image = images.photos[0]["image"]["web"]);
    },
    getScores(data){
      fetch(data["_links"]["ua:scores"]["href"])
      .then(response => response.json())
      .then(scores => this.scores = scores.categories);
    },
    getCategoryScores(categoryIndex) {
      const scores = []
      // const urls = this.urbanAreas.map(area => area.href);
      // const requests = urls.map(url => fetch(url).then(response => response.json()));
      //
      // Promise.all(requests).then(responses => {
      //   const scoreUrls = responses.map(response => response["_links"]["ua:scores"]["href"]);
      //
      //   const scoreRequests = scoreUrls.map(url => fetch(url).then(response => response.json()));
      //
      //   Promise.all(scoreRequests).then(scoreResponses => {
      //     scoreResponses.forEach(value => {
      //       let score = value.categories[categoryIndex]["score_out_of_10"];
      //       let name = value.summary.split(" ")[1];
      //       scores.push({"name": name, "score": score});
      //     })
      //   });
      //   this.categoryScores = scores;
      //   this.getTopTen();
      // });

      this.urbanAreas.forEach((urbanArea) => {
        fetch(urbanArea["href"])
        .then(response => response.json())
        .then(data => fetch(data["_links"]["ua:scores"]["href"]))
        .then(response => response.json())
        .then(values => {
          let score = values.categories[categoryIndex]["score_out_of_10"]
          scores.push({"name": urbanArea.name, "score": score.toFixed(2)})
        }).then(() => {
          this.categoryScores = scores
          this.getTopTen();
        });
      }, [0]);

    },
    getTopTen(){
      const sortedScores = this.categoryScores.sort((a, b) => {
        return b.score - a.score;
      });
      this.categoryTopTen = sortedScores.slice(0, 10);
    }
  }
}
</script>

<style lang="css" scoped>

#flex-container {
  display: flex;
  align-items: center;
  flex-direction: column;
}

nav {
  display: flex;
  flex-direction: row;
  justify-content: center;
  width: 100%;
  /* padding: 10px; */
  background-color: rgb(255, 231, 216);
}

urban-areas-list, category-list {
  width: inherit;
}

h1 {
  font-family: 'Nova Round', cursive;
  font-size: 5em;
  cursor: pointer;
  padding: 0;
}

option {
  width: inherit;
}

img {
  width: 100%;
  margin: 0;
  padding: 0;
}
@media all and (max-width: 1050px) {
  h1 {
    font-size: 3em;
  }
}

@media all and (max-width: 1000px){
  h1 {
    font-size: 3em;
  }
}

@media all and (max-width: 700px){
  h1 {
    font-size: 2em;
  }
}


</style>
