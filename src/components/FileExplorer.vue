<template>
  <section class='container'>
    <div class='row'>
      <div class='col-md-12'>
        <table class='table small table-striped'
               v-if='doesGithubHaveFiles'>
          <caption>{{ path }}</caption>
          <thead class='thead-light'>
            <tr>
              <th>Name</th>
              <th class='text-right'>
                <button class='btn btn-primary btn-xs'
                        @click='goOneDirectoryUp()'
                        v-if='isNotRoot'>Go Back</button>
              </th>
            </tr>
          </thead>

          <tbody>
            <tr v-for='file in files'>
              <td>
                <div class='file' v-if='isFileTypeFile(file)'>
                  <Icon name='file-o'></Icon>
                  {{ file.name }}
                </div>

                <div class='directory' v-if='isFileTypeDirectory(file)'>
                  <a @click.prevent='goInsideFile(file.path)'>
                    <Icon name='folder-o'></Icon>
                    <u>{{ file.name }}</u>
                  </a>
                </div>
              </td>
              <td class='text-right'>
                <a :href='file.download_url' download v-if='isFileTypeFile(file)'>
                  <Icon name='cloud-download'></Icon>
                </a>
              </td>
            </tr>
          </tbody>
        </table>

        <div class="alert alert-warning" role="alert" v-else>
          {{ alertMessage }}
        </div>
      </div>
    </div>
   </section>
</template>

<script type='text/javascript'>
  import Icon from 'vue-awesome/components/Icon';

  export default {
    name: 'FileExplorer',
    components: {
      Icon,
    },
    data() {
      return {
        files: [],
        path: '/',
        alertMessage: 'Please enter valid full repository to fetch data.'
      };
    },
    props: {
      username: {
        type: String,
        required: true,
      },
      repo: {
        type: String,
        required: true,
      },
    },
    computed: {
      fullRepoUrl() {
        return this.username + '/' + this.repo;
      },
      isNotRoot() {
        return this.path !== '/';
      },
      doesGithubHaveFiles() {
        return this.files.length > 0;
      },
    },
    methods: {
      getGithubFiles() {
        const githubUrl = 'https://api.github.com/repos/' +
                          this.fullRepoUrl +
                          '/contents' +
                          this.path;
        let allFiles = [];

        this.$http.get(githubUrl).then(response => {
          this.files = response.body;
        }, response => {
          this.alertMessage = response.body.message;
        });
      },
      goOneDirectoryUp() {
        debugger
        console.log(this.path);
      },
      goInsideFile(path) {
        this.path = path;
        this.getGithubFiles();
      },
      isFileTypeFile(file) {
        return file.type === 'file';
      },
      isFileTypeDirectory(file) {
        return file.type === 'dir';
      },
    },
    created() {
      if (this.username && this.repo) this.getGithubFiles();
    },
    watch: {
      repo(newVal, oldVal) {
        if (newVal !== oldVal) this.getGithubFiles();
      }
    },
  };
</script>

<style scoped="">

</style>
