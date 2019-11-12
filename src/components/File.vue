<template>
  <div class="panel-block" >
    <div class="tile is-ancestor is-gapless">
      <div class="tile is-vertical is-parent">
        <div class="tile is-child has-text-left">
          <span class="icon is-left">
            <i
              class="fa fa-circle"
              :style="{
                color: calculateBackgroundColor(file.complexityRatio)
              }"
            ></i>
          </span>
          <span>{{ file.name }} ({{ file.complexityRatio | formatFloat }})</span>
          <a><span class="icon is-pulled-right link" @click="showDetails = !showDetails"><i class="fa fa-chevron-down"></i></span></a>
        </div>
        <div class="tile is-child has-text-left" v-show="showDetails"
        v-for="(violations, functionName) in file.functions"
        v-bind:key="functionName"
        >
        <p>{{ functionName }}:</p>
        <table class="table is-striped is-bordered">
          <thead>
            <tr>
              <td></td>
              <td>Rule</td>
              <td>Amount</td>
              <td>Threshold</td>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(violation, index) in violations"
              v-bind:key="index"
            >
              <td
              :style="{
                backgroundColor: calculateBackgroundColor(
                  violation.value / violation.threshold
                )
              }"
              ><span class="icon"></span></td>
              <td>{{ violation.rule }}</td>
              <td>{{ violation.value }}</td>
              <td>{{ violation.threshold }}</td>
            </tr>
          </tbody>
        </table>
        </div>
      </div>
    </div>
    </div>
</template>

<script>
import {} from 'bulma'
export default {
  name: "File",
  data: function() {
    return {
      showDetails: false
    };
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
  props: ["file"],
  filters: {
    formatFloat: function(value) {
      if (!value) return ''
      return Math.round(value * 100) / 100;
    }
  }
};
</script>

<style lang="scss">
.tile.is-gapless {
  
  .tile.is-vertical>.tile.is-child {
    margin-bottom: 0 !important;
  }
  
  .notification {
    border-radius: 0;
  }
}
</style>