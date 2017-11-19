<template>
  <section class='container'>
    <div class='row'>
      <div class='col-md-12'>
        <nav aria-label="breadcrumb" role="navigation">
          <ol class="breadcrumb">
            <li class="breadcrumb-item" v-for="(crumb, key) in fetchPathBreadCrumbs()">
              <a href="#">{{ key }} : {{ crumb }}</a>
            </li>
          </ol>
        </nav>
      </div>
    </div>
    <div class='row'>
      <div class='col-md-12'>
        <table class='table small table-striped'
               v-if='doesGithubHaveFiles'>
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
          this.files = this.sortGithubFiles(response.body);
        }, response => {
          this.alertMessage = response.body.message;
        });
      },
      sortGithubFiles(files) {
        return files.slice(0).sort(function (a, b) {
          // 1. Sort by Type i.e directory first then files
          // 2. Sort by name within directory and files
          if (a.type !== b.type) {
             return (a.type === 'dir') ? -1 : 1;
           } else {
             return (a.name < b.name) ? -1 : 1;
           }
        });
      },
      goOneDirectoryUp() {
        let splittedPath = this.path.split('/');
        let length = splittedPath.length;
        let splittedArray = splittedPath.splice(0, length - 1)
        this.path = (splittedArray.length > 1 ) ? splittedArray.join('/') : '/';
        this.getGithubFiles();
      },
      goInsideFile(path) {
        this.path = '/' + path;
        this.getGithubFiles();
      },
      isFileTypeFile(file) {
        return file.type === 'file';
      },
      isFileTypeDirectory(file) {
        return file.type === 'dir';
      },
      fetchPathBreadCrumbs () {
        let breadCrumbs = ['root'];

        if (this.path !== '/') {
          let splittedArray = this.path.split('/');
          splittedArray.splice(0, 1);

          if (splittedArray.length > 0) {
            breadCrumbs = breadCrumbs.concat(splittedArray);
          }
        }
        return breadCrumbs;
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
