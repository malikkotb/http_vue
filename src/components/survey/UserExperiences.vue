<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button @click="loadExperiences"
          >Load Submitted Experiences</base-button
        >
      </div>
      <p v-if="isLoading">Loading...</p>
      <p v-else-if="!isLoading && error">
        {{ error }}
      </p>
      <p v-else-if="!isLoading && (!results || results.length === 0)">
        No stored experiences found. Start adding some survery results first.
      </p>
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
import SurveyResult from "./SurveyResult.vue";

export default {
  components: {
    SurveyResult,
  },
  // props: ['results'],
  data() {
    return {
      results: [],
      isLoading: false,
      error: null,
    };
  },
  methods: {
    loadExperiences() {
      // default method is: 'GET'
      //no body and no headers, when we want to get data

      // fetch() returns a Promise
      // fetch retuns an object on which we can listen for the data once it is there,
      // to then set up code, that will be executed when the data is there.
      // We set up such a listener, by adding a then() method

      // we add another then() method (because repsonse.json() also returns a Promise)
      // the 2nd then() ethod will be triggered when the returned promise is done

      this.isLoading = true;
      this.error = null;
      fetch(
        "https://vue-http-da4db-default-rtdb.europe-west1.firebasedatabase.app/surveys.json"
      )
        .then((response) => {
          // parse and work with the response
          if (response.ok) {
            return response.json();
          }
        })
        .then((data) => {
          this.isLoading = false;
          const results = [];
          for (const id in data) {
            results.push({
              id: id,
              name: data[id].name,
              rating: data[id].rating,
            });
          }
          this.results = results;
          //  console.log(data)
        })
        .catch((error) => {
          // catch takes a function, that is triggered whenever any error occurrs related
          // to the previous then() blocks
          this.isLoading = false;
          this.error = "Failed to fetch data - please try again later.";
          console.log(error);
        });
    },
  },

  mounted() {
    // The mounted() hook of this component is called when this exact component gets mounted!
    this.loadExperiences();
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
