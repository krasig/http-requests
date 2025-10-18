<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiances">Load Submitted Experiences</base-button>
      </div>
      <p v-if="isLoading">Loading ...</p>
      <p v-else-if="!isLoading && !error && (!results || results.length === 0)">No submitted experiences found.</p>
      <p v-else-if="!isLoading && error">{{error}}</p>
      <ul v-else-if="!isLoading && results && results.length > 0">
        
        <survey-result v-for="result in results" :key="result.id" :name="result.name"
          :rating="result.rating"></survey-result>
      </ul>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';

export default {
  
  components: {
    SurveyResult,
  },
  data() {
    return {
      results: [],
      isLoading:false,
      error:null,
    };
  },
  methods: {
    loadExperiances() {
      this.isLoading = true;
      this.error = null;
      fetch('https://vute-tests-default-rtdb.europe-west1.firebasedatabase.app/surveys.json')
        .then((response)=>{
          if (response.ok) {
            return response.json();
          }
        })
        .then((data) => {
          console.log(data);
          this.isLoading = false;
          const surveyResults = [];
          for(const id in data){
            surveyResults.push({
              id: id,
              name: data[id].name,
              rating: data[id].rating,
            });
          }
          this.results = surveyResults;
        })
        .catch((error)=>{
          console.log(error);
          this.isLoading = false;
          this.error = 'Failed to fetch data. Please try again later.';
        });
        
    },
  },
  mounted() {
    this.loadExperiances(); 
  },
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>