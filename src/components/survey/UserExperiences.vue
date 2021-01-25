<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences">Load Submitted Experiences</base-button>
      </div>
      <h3 v-if="isLoading">Loading...</h3>
      <ul v-else-if="!isLoading && results && results.length > 0">
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
      <p v-else-if="!isLoading && isError">{{ isError }}</p>
      <h4 v-else-if="!isLoading && (!results || results.length === 0)">No Stored Experiences found. Please add the survey results to see.</h4>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';
import url from "../../url.js";

export default {
  // props: ['results'],
  components: {
    SurveyResult,
  },
  data() {
    return {
      results: [],
      isLoading: false,
      isError: null
    }
  },
  mounted() {
    this.loadExperiences();
  },
  methods: {
    loadExperiences() {
      this.isLoading = true;
      this.isError = null;
      fetch(url.url)
      .then(response => {
        if(response.ok) {
          return response.json();
        }
      }).then(data => {
        this.isLoading = false;
        const results = [];
        for(const id in data) {
          results.push({
            id: id,
            name: data[id].name,
            rating: data[id].rating
          })
        }
        this.results = results;
      }).catch(error => {
        console.log(error);
        this.isLoading = false;
        this.isError = 'Failed to fetch the data. Please try again after some time';
      });
    }
  }
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>