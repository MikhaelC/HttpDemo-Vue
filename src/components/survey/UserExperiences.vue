<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences"
          >Load Submitted Experiences</base-button
        >
      </div>
      <p v-if="!isLoading && error">{{ error }}</p>
      <p v-else-if="isLoading && !error">Loading...</p>
      <p v-else-if="!isLoading && (!results || results.length === 0)">
        No stored experiences found!
      </p>
      <ul v-else-if="!isLoading && results && results.length > 0">
        <survey-result
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        ></survey-result>
      </ul>
    </base-card>
  </section>
</template>

<script>
// import func from '../../../vue-temp/vue-editor-bridge';
import SurveyResult from './SurveyResult.vue';

export default {
  components: {
    SurveyResult
  },
  data() {
    return {
      results: [],
      isLoading: null,
      error: null
    };
  },
  methods: {
    loadExperiences() {
      this.isLoading = true;
      this.error = null;
      fetch(
        'https://vue-http-demo-a31a4-default-rtdb.firebaseio.com/survey.json'
      )
        .then(response => {
          if (response.ok) {
            return response.json();
          }
        })
        .then(data => {
          this.isLoading = false;
          let results = [];
          for (let id in data) {
            results.push({
              id: id,
              name: data[id].userName,
              rating: data[id].rating
            });
            // console.log(results);
            this.results = results;
          }
        })
        .catch(error => {
          console.log(error);
          this.isLoading = false;
          this.error = 'Failed to fetch data - please try again !';
        });
    }
  },
  mounted() {
    this.loadExperiences();
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
