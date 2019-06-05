<template>
  <div id="app">

    <form v-on:submit="handleSubmit">
      <h1>Enter A Word To Find Synonyms</h1>
      <input type="text" placeholder="ex. 'Angry'">
      <button class="submit-button" v-on:click="handleSubmit">SUBMIT</button>
    </form>

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
      e.preventDefault();
      let container = e.target.parentElement.parentElement.children[1];
      container.children[0].innerText = `loading..`;
      let word = e.target.parentElement.children[1].value;

      if (!word) {
        container.innerHTML = `<h3>Please Enter A Word Before Searching</h3><div></div>`;
        return null;
      }

      let response = await this.fetchSynonyms(word);
      let data = await response.json();

      this.determinePath(container, word, data);
    },

    handleClick: async function (e) {
      let container = e.target.parentElement.parentElement;
      container.children[0].innerText = `loading..`;
      container.children[1].innerHTML = '';
      let word = e.target.innerText;

      let response = await this.fetchSynonyms(word);
      let data = await response.json();

      this.determinePath(container, word, data);
    },

    determinePath: function (container, searchWord, responseData) {
      if (responseData[0] && responseData[0].meta) {
        this.gatherSynonyms(container, searchWord, responseData);

      } else if (responseData.length) {
        this.appendSuggestions(container, searchWord, responseData);

      } else {
        container.innerHTML = `<h3>No Results Found For '${searchWord}', try again!</h3><div></div>`;

      }
    },

    fetchSynonyms: async function (word) {
      let url = `https://www.dictionaryapi.com/api/v3/references/thesaurus/json/${word}?key=`;
      return await fetch(url + process.env.VUE_APP_APIKEY);
    },

    gatherSynonyms: function (container, searchWord, responseData) {
      let synonyms = [];

      responseData.map(collection => collection.meta.syns.map(list => {
        list.forEach(word => synonyms.push(word));
      }));

      this.appendSynonyms(container, searchWord, synonyms);
    },

    appendSynonyms: function (container, searchWord, synonyms) {
      container.children[0].innerText = `${synonyms.length} Synonyms Found For '${searchWord}':`;
      
      let newHTML = synonyms.map(synonym => this.buildButton(synonym));
      container.children[1].innerHTML = newHTML.join('');
      this.handleButtons();
    },

    appendSuggestions: function (container, word, suggestions) {
      container.children[0].innerText = `No Results Found For '${word}', Did You Mean:`;

      let newHTML = suggestions.map(word => this.buildButton(word));
      container.children[1].innerHTML = newHTML;
      this.handleButtons();
    },

    buildButton: function (synonym) {
      return `<button class="synonym">${synonym}</button>`;
    },

    handleButtons: function () {
      let buttons = this.$el.querySelectorAll('.synonym');
      buttons.forEach(button => button.addEventListener("click", this.handleClick ));
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

input {
  box-sizing: border-box;
  width: 100%; height: 50px;
  padding-left: 20px;
  /**/
  border: solid black 2px;
  border-radius: 50px;
  /**/
  font-size: 24px;
  transition: all .2s ease;
}

input::placeholder {
  font-size: 24px;
  transition: all .2s ease;
}

input:hover { border-color: grey; }

input:active { opacity: 0.5; }

input:focus { outline: none; }

input:focus::placeholder { color: white; }

.submit-button {
  box-sizing: border-box;
  width: 100%; height: 50px;
  margin-top: 20px;
  font-size: 24px;
  color: white;
  background: black;
  border-radius: 50px;
  transition: all .2s ease;
}

.submit-button:hover { 
  background: grey;
  cursor: pointer; 
}

.submit-button:active { opacity: 0.5; }

.submit-button:focus { outline: none; }

.synonym {
  padding: 10px;

  font-size: 16px;
  font-weight: 600;
  color: grey;

  border: none;
  border-radius: 5px;

  transition: all .2s ease;
}

.synonym:hover {
  background: grey;
  color: white;
  cursor: pointer;
}

.synonym:active { opacity: 0.5; }

.synonym:focus { outline: none; }

</style>
