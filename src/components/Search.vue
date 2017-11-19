<template>
  <form id="searchInput" class="form-inline" @submit.prevent="fetchRepoInformation()">
    <div class="form-group">
      <label for="repoFullTitle" class="sr-only">{{ inputField.label }}</label>
      <input type="text"
             class="form-control"
             v-model="inputField.repoFullTitle"
             @keyup="enableButtonState"
             id="repoFullTitle"
             placeholder="enter full repo name">
    </div>
    <button type="submit"
            class="btn btn-primary"
            v-bind:disabled="shouldSubmitButtonBeDisabled">
            {{ button.text }}</button>
  </form>
</template>

<script type="text/javascript">
  export default {
    name: 'SearchComponent',
    data() {
      return {
        inputField: {
          repoFullTitle: 'alokpant/vuejs-todoapp',
          label: 'Repository Full Title',
        },
        button: {
          text: 'Fetch Files',
          isDisabled: false,
        },
      };
    },
    computed: {
      shouldSubmitButtonBeDisabled() {
        return this.button.isDisabled;
      },
    },
    methods: {
      fetchRepoInformation() {
        this.disableButtonState();
        this.$emit('form-submitted', this.inputField.repoFullTitle.trim());
      },
      disableButtonState() {
        this.button.isDisabled = true;
        this.button.text = 'Fetching';
      },
      enableButtonState() {
        this.button.isDisabled = false;
      },
    },
  };
</script>
