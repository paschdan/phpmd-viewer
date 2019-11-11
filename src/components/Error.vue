<template>
  <article class="message is-danger" v-if="errors.length > 0">
    <div class="message-header">
      <p>An Error occured</p>
      <button class="delete" aria-label="delete" @click="closeError"></button>
    </div>
    <div class="message-body">
      <p v-for="(error, index) in errors" v-bind:key="index">
        {{ error }}
      </p>
    </div>
  </article>
</template>

<script>
export default {
  methods: {
    closeError: function() {
      this.$emit("closeError");
    }
  },
  props: ["errors"]
};
</script>