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

export default {

  name: 'app',

  methods: {

    handleSubmit: async function (e) {
      let container = e.target.parentElement.parentElement.children[1];
      container.children[0].innerText = `loading..`;
      let word = e.target.parentElement.children[1].value;

      let response = await this.fetchSynonyms(word);
      let data = await response.json();
      // 
      if (data[0] && data[0].meta) {
        let synonyms = [];
        data.map(word => word.meta.syns.map(list => {
          list.forEach(word => synonyms.push(word));
        }));
        this.appendSynonyms(container, word, synonyms);
      }
    },

    fetchSynonyms: async function (word) {
      let url = `https://www.dictionaryapi.com/api/v3/references/thesaurus/json/${word}?key=`;
      return await fetch(url + process.env.VUE_APP_APIKEY);
    },

    appendSynonyms: function (container, word, synonyms) {
      container.children[0].innerText = `${synonyms.length} Synonyms Found For '${word}':`;
      
      let newHTML = synonyms.map(word => this.buildButton(word));
      container.children[1].innerHTML = newHTML;
    },

    buildButton: function (word) {
      return `<button class="synonym">${word}</button>`;
    }

  }

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
