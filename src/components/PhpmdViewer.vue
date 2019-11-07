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
   <nav class="panel" v-for="(file, index) in phpmdData" v-bind:key="index">
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
                <tr v-for="(violation) in violations" v-bind:key="violation.rule">
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
  methods: {
    processFile: function(event) {
      let file = event.target.files[0];
      const reader = new FileReader();
      const self = this
      reader.onload = function(e) {
        const data = JSON.parse(e.target.result)
        const mapped = data.files.reduce((result, file) => {
        const name = file.file;
        if(self.filterName)
        {
          if (!name.includes(this.filterName)) {
            return result
          }
        }
        const functions = {}
        file.violations.forEach(violation => {
          if(!functions[violation.function]) {
            functions[violation.function] = []
          }
          const numbers = violation.description.match(/^\d+|\d+\b|\d+(?=\w)/g);
          const violationObject = {
            rule: violation.rule,
            value: numbers[0],
            threshold: numbers[1],
          }
          functions[violation.function].push(violationObject)
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
