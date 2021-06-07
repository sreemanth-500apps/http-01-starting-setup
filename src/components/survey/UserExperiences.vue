<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadexperiences">Load Submitted Experiences</base-button>
      </div>
      <p v-if="isloading"> Loading...</p> 
      <p v-else-if="!isloading && error"> {{ error }}</p>
      <p v-else-if="!isloading && (!results || results.length === 0)">No stored experiences found</p>
      <ul v-else>
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
import SurveyResult from './SurveyResult.vue';

export default {
  components: {
    SurveyResult,
  },
  data() {
    return {
      results: [],
      isloading: false,
      error: null,
    };
  },
  methods: {
    loadexperiences() {
      this.isloading = true;
      this.error=null;
      fetch('https://vue-course-fbade-default-rtdb.firebaseio.com/surveys.json').then(
        (response) => {
          if (response.ok) {
            return response.json();
          }
        }
      ).then((data) => {
          this.isloading=false;
          const results = [];
          for (const id in data) {
            results.push({ id: id, name: data[id].name, rating: data[id].rating,});
          }
          this.results=results;
      })
      .catch((_error) => {
        console.log(_error);
        this.isloading=false;
        this.error = 'Failed to fetch data !! Please try again.'
      });
    },
  },
  mounted() {
    this.loadexperiences();
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