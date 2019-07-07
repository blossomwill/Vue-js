<template>
  <form v-on:submit.prevent="submitForm">
    <div>
      <label for="username">id: </label>
      <input id="username" type="text" v-model="username">
    </div>
    <div>
      <label for="password">pw: </label>
      <input id="password" type="password" v-model="password">
    </div>
    <button type="submit">loin</button>

  </form>
</template>

<script>
import axios from 'axios';
export default {
  data: function() {
    return {
      username: '',
      password: '',
    }
  },
  methods: {
    submitForm: function() {
      //event.preventDefault();
      console.log(this.username, this.password);
      let url = 'https://jsonplaceholder.typicode.com/users';
      let data = {
        username: this.username,
        password: this.password,
      }
      axios.post(url, data)
      .then(function(response){
        console.log(response);
      })
      .catch(function(response){
        console.log(response);
      });
    }
  }
}
</script>

<style>

</style>
