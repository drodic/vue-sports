<template>
  <div v-if="!loading && data && data.length">
    <span v-for="post of data" v-bind:key="post.code">
        {{ post.name }}, 
    </span>
  </div>

  <p v-if="loading">Still loading..</p>
  <p v-if="error"></p>
</template>

<script>
import { ref, onMounted } from "vue";
export default {
  name: "GridComponent",
  props: {},
  setup() {
    const data = ref(null);
    const loading = ref(true);
    const error = ref(null);

    function fetchData() {
      loading.value = true;
      // I prefer to use fetch
      // you can use use axios as an alternative
      return fetch("https://v3.football.api-sports.io/countries", {
        method: "GET",
        headers: {
          "x-rapidapi-host": "v3.football.api-sports.io",
          "x-rapidapi-key": "55099e25e8b1ef272e1ad29e85c4a7ac",
        },
      })
        .then((res) => {
          // a non-200 response code
          if (!res.ok) {
            // create error instance with HTTP status text
            const error = new Error(res.statusText);
            error.json = res.json();
            throw error;
          }
          return res.json();
        })
        .then((json) => {
          // set the response data
          data.value = json.response;
        })
        .catch((err) => {
          error.value = err;
          // In case a custom JSON error response was provided
          if (err.json) {
            return err.json.then((json) => {
              // set the JSON response message
              error.value.message = json.message;
            });
          }
        })
        .then(() => {
          loading.value = false;
        });
    }

    onMounted(() => {
      fetchData();
    });

    return {
      data,
      loading,
      error,
    };
  },
};
</script>

<style lang="scss" scoped></style>
