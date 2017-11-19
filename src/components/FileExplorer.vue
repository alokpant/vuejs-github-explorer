<template>
  <section class='container'>
    <div class='row' v-if='doesGithubHaveFiles'>
      <div class='col-md-12'>
        <nav aria-label="breadcrumb" role="navigation" class="custom-breadcrumbs">
          <ol class="breadcrumb">
            <li class="breadcrumb-item"
                v-for="(crumb, key) in fetchPathBreadCrumbs()"
                v-bind:key="key"
                v-bind:class="isActive(key)"
                v-bind:aria-current="currentPage(key)">
              <a class='custom-breadcrumbs--links'
                 v-on:click="traveseDirectoryUpwards(key)">
                 {{ crumb }}
               </a>
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
                        @click='traveseDirectoryUpwards()'
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
      doesGithubHaveFiles() {
        return this.files.length > 0;
      },
    },
    methods: {
      isNotRoot() {
        return this.path !== '/';
      },
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
      traveseDirectoryUpwards(key) {
        if (this.isNotRoot() && key != 0) {
          var splittedArray = this.splitPathIntoArray();
          var arrayLength = splittedArray.length;

          let deleteStartPosition;

          if (typeof key === typeof undefined) {
            deleteStartPosition = arrayLength - 1;
          } else {
            deleteStartPosition = key + 1;
          }

          let deleteCount = arrayLength - deleteStartPosition;
          splittedArray.splice(deleteStartPosition, deleteCount)

          if (splittedArray.length > 1) {
            this.path = splittedArray.join('/');
          } else {
            this.path = '/';
          }
        } else {
          this.path = '/';
        }

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
          let splittedArray = this.splitPathIntoArray();
          splittedArray.splice(0, 1);

          if (splittedArray.length > 0) {
            breadCrumbs = breadCrumbs.concat(splittedArray);
          }
        }
        return breadCrumbs;
      },
      splitPathIntoArray() {
        return this.path.split('/');
      },
      isActive(key) {
        return this.checkStatus(key, 'active');
      },
      checkStatus(key, statusClass) {
        if (this.isNotRoot()) {
          const pathLengthWithSpaces = this.splitPathIntoArray();
          // - 2 because of extra space that is added
          // at top of path.
          if (key !== (pathLengthWithSpaces.length - 1)) {
            statusClass = '';
          }
        }

        return statusClass;
      },
      currentPage(key) {
        return this.checkStatus(key, 'page');
      }
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
  .custom-breadcrumbs .breadcrumb-item.not(.active) .custom-breadcrumbs--links {
    color: #007BE9;
  }
</style>
