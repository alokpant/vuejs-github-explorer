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
          <SearchComponent v-on:form-submitted="fetchGithubFiles"></SearchComponent>
        </div>
      </div>

      <div class="container">
        <div class="col-sm-8 mx-auto">
          <FileExplorer :username="username" :repo="repoName"></FileExplorer>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
  import SearchComponent from './components/Search';
  import FileExplorer from './components/FileExplorer';

  export default {
    name: 'app',
    data() {
      return {
        fullRepoName: '',
        username: '',
        repoName: '',
      };
    },
    components: {
      SearchComponent,
      FileExplorer,
    },
    methods: {
      fetchGithubFiles(fullRepoName) {
        const splitData = fullRepoName.split('/');
        this.fullRepoName = fullRepoName;
        this.username = splitData[0];
        this.repoName = splitData[1];
        // console.group('Repository Information');
        // console.log('Repository Full Path: ' + this.fullRepoName);
        // console.log('Username: ' + this.username);
        // console.log('Repository Name: ' + this.repoName);
        // console.groupEnd('Repository Information');
      },
    },
  };
</script>

<style> @import "bootstrap"; </style>
