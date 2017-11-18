<template>
  <form id="searchInput" class="form-inline" @submit.prevent="fetchRepoInformation()">
    <div class="form-group">
      <label for="repoFullTitle" class="sr-only">{{ label }}</label>
      <input type="text"
             class="form-control"
             id="repoFullTitle"
             value="email@example.com">
    </div>
    <button type="submit"
            class="btn btn-primary"
            v-bind:disabled="button.isDisabled">
            {{ button.text }}</button>
  </form>
</template>

<script type="text/javascript">
  export default {
    name: 'SearchComponent',
    data() {
      return {
        label: 'Repository Full Title',
        button: {
          text: 'Fetch Files',
          isDisabled: false,
        },
      };
    },
    methods() {
      return {
        fetchRepoInformation() {
          debugger;
        },
      };
    },
  };
</script>
