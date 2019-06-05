<template>
  <div id="app">

    <div>
      <h1>Enter A Word To Find Synonyms</h1>
      <input type="text" placeholder="ex. 'Angry'">
      <button class="submit-button" v-on:click="handleSubmit">SUBMIT</button>
    </div>

    <div class="container">
      <h3></h3>
      <div></div>
    </div>

  </div>
</template>

<script>

  // process.env.VUE_APP_APIKEY

export default {

  name: 'app',

}

</script>

<style>

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  width: 70%;
  margin: auto;
  margin-top: 60px;
  /**/
  display: flex;
  flex-direction: column;
  align-items: center;
}

</style>
