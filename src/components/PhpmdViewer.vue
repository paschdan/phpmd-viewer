<template>
  <section class="section">
  <div class="container">
   <h1 class="title">PHPMD Violations</h1>
   <div v-if="!phpmdData">
   <h2>Upload a File phpmd json file</h2>
   <input type="file" @change="processFile($event)">
   </div>
   <div v-if="phpmdData">
   <input class="input" type="text" placeholder="Filter Filename" v-model="filterName">
   <hr>
   <nav class="panel" v-for="(file, index) in filteredData" v-bind:key="index">
     <p class="panel-heading">
        {{file.name}}
    </p>
        <div class="panel-block" v-for="(violations, functionName ) in file.functions" v-bind:key="functionName">
            <p>{{functionName}}:</p>
            <table class="table is-striped is-bordered">
              <thead>
                <tr>
                  <td>Rule</td>
                  <td>Amount</td>
                  <td>Threshold</td>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(violation) in violations" v-bind:key="violation.rule" :style="{backgroundColor: calculateBackgroundColor(violation.value, violation.threshold)}">
                  <td>{{violation.rule}}</td>
                  <td>{{violation.value}}</td>
                  <td>{{violation.threshold}}</td>
                </tr>
              </tbody>
            </table>
        </div>
   </nav></div>
  </div>
  </section>
</template>

<script>
export default {
  name: 'PhpmdViewer',
  data: function() {
    return {
      phpmdData: '',
      rawData: '',
      filterName: ''
    }
  },
  computed: {
    filteredData: function() {
        return this.phpmdData.filter(item => {
          return item.name.includes(this.filterName)
        })
    },
  }, methods: { 
    calculateBackgroundColor: function(value, threshold) {
      const ratio = value / threshold
      if (ratio > 1.2) {
        return 'darkRed'
      }
      if (ratio > 1) { 
        return 'red'
      }
      if (ratio > 0.8) {
        return 'DarkOrange'
      }
      if (ratio > 0.6) {
        return 'orange'
      }
      if (ratio > 0.3) {
        return 'yellow'
      }
      return 'green'
    },
    processFile: function(event) {
       let file = event.target.files[0];
      const reader = new FileReader();
      const self = this
      reader.onload = function(e) {
        const data = JSON.parse(e.target.result)
        self.rawData = data
        const mapped = data.files.reduce((result, file) => {
        const name = file.file;
        const functions = {}
        file.violations.forEach(violation => {
          if(violation.function && !functions[violation.function]) {
            functions[violation.function] = []
          }
          if(violation.method && !functions[violation.method]) {
            functions[violation.method] = []
          }
          if(violation.class && !violation.method && !functions[violation.class]) {
            functions[`Class_${violation.class}`] = []
          }
          const numbers = violation.description.match(/^\d+|\d+\b|\d+(?=\w)/g);
          const violationObject = {
            rule: violation.rule,
            value: numbers[0],
            threshold: numbers[1],
          }
          if(violation.function) {
            functions[violation.function].push(violationObject)
          }
          if(violation.method) {
            functions[violation.method].push(violationObject)
          } 
          if(violation.class && !violation.method) {
            functions[`Class_${violation.class}`].push(violationObject)
          }
        });
        result.push({name, functions})
        return result
      }, [])
      self.phpmdData = mapped;
      }
      reader.readAsText(file);
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
