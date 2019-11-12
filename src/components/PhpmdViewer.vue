<template>
  <section class="section">
    <div class="container">
      <h1 class="title">PHPMD Violations</h1>
      <Error :errors="errors" @closeError="errors = []" />
      <Uploader
        v-if="!phpmdData"
        @analyzedData="retrieveAnalyzedData"
        @Error="handleError"
      />
      <div v-if="phpmdData">
        <div class="panel">
          <p class="panel-heading">Violations in File</p>
          <div class="panel-block">
            <p class="control has-icons-left">
              <input
                class="input"
                type="text"
                placeholder="Filter Filename"
                v-model="filterName"
              />
              <span class="icon is-left">
                <i class="fas fa-search" aria-hidden="true"></i>
              </span>
            </p>
          </div>
          <File
            v-for="(file, index) in filteredData"
            v-bind:key="index"
            :file="file"
          />
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import File from "./File";
import Error from "./Error";
import Uploader from "./Uploader";

export default {
  name: "PhpmdViewer",
  data: function() {
    return {
      phpmdData: "",
      rawData: "",
      filterName: "",
      status: "init",
      errors: [],
      details: {}
    };
  },
  computed: {
    filteredData: function() {
      return this.phpmdData.filter(item => {
        return item.name.includes(this.filterName);
      });
    }
  },
  methods: {
    handleError: function(error) {
      this.errors = [error];
    },
    retrieveAnalyzedData: function(data) {
      this.phpmdData = data;
    }
  },
  components: {
    File,
    Error,
    Uploader
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
