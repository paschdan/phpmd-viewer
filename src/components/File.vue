<template>
  <nav class="panel" >
    <p
      class="panel-heading"
      :style="{
        backgroundColor: calculateBackgroundColor(file.complexityRatio)
      }"
      @click="showDetails = !showDetails"
    >
      {{ file.name }}
    </p>
    <div v-show="showDetails">
      <div
        class="panel-block"
        v-for="(violations, functionName) in file.functions"
        v-bind:key="functionName"
      >
        <p>{{ functionName }}:</p>
        <table class="table is-striped is-bordered">
          <thead>
            <tr>
              <td>Rule</td>
              <td>Amount</td>
              <td>Threshold</td>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="violation in violations"
              v-bind:key="violation.rule"
              :style="{
                backgroundColor: calculateBackgroundColor(
                  violation.value / violation.threshold
                )
              }"
            >
              <td>{{ violation.rule }}</td>
              <td>{{ violation.value }}</td>
              <td>{{ violation.threshold }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </nav>
</template>

<script>
export default {
  name: "File",
  data: function() {
      return {
        showDetails: false
      }
  },
  methods: {
    calculateBackgroundColor: function(ratio) {
      if (ratio > 1.2) {
        return "darkRed";
      }
      if (ratio > 1) {
        return "red";
      }
      if (ratio > 0.8) {
        return "DarkOrange";
      }
      if (ratio > 0.6) {
        return "orange";
      }
      if (ratio > 0.3) {
        return "yellow";
      }
      return "green";
    }
  },
  props: ["file"]
};
</script>