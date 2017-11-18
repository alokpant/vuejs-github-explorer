<template>
  <div id="app">
    <main role="main">
      <div class="jumbotron">
        <div class="col-sm-8 mx-auto">
          <h1>Github File Explorer</h1>
          <p>This is based on tutorial
            <a href="https://scotch.io/tutorials/create-a-github-file-explorer-using-vue-js">
              https://scotch.io/tutorials/create-a-github-file-explorer-using-vue-js
            </a>.
            However tutorial is based on Vue1 and solution below is based on Vue2.
          </p>
          <p>To start using simply type repository full name
            (example: <i>alokpant/vuejs-github-explorer</i>) and click on search.
          </p>
        </div>
        <div class="col-sm-8 mx-auto">
          <SearchComponent />
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import SearchComponent from './components/Search';

export default {
  name: 'app',
  components: {
    SearchComponent,
  },
};
</script>

<style>
  @import "bootstrap";

</style>
