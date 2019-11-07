<template>
  <section class="section">
  <div class="container">
   <h1 class="title">PHPMD Violations</h1>
    <article class="message is-danger" v-if="errors.length > 0">
    <div class="message-header">
      <p>An Error occured</p>
      <button class="delete" aria-label="delete" @click="errors = []; status='init'"></button>
    </div>
    <div class="message-body">
      <p v-for="(error, index) in errors" v-bind:key="index">
        {{error}}
      </p>
    </div>
  </article>
   <div v-if="!phpmdData">
   <h2>Upload a File phpmd json file</h2>
   <input type="file" @change="processFile($event)" accept=".json, .JSON">

   </div>
   <div v-if="status == 'analyzing'">
     Analizing the file...
   </div>
   <div v-if="phpmdData">
   <input class="input" type="text" placeholder="Filter Filename" v-model="filterName">
   <hr>
   <nav class="panel" v-for="(file, index) in filteredData" v-bind:key="index">
     <p class="panel-heading" :style="{backgroundColor: calculateBackgroundColor(file.complexityRatio)}" @click='toggleDetails(file.name)'>
        {{file.name}}
    </p>
    <div v-show="details[file.name]">
        <div
         class="panel-block"
         v-for="(violations, functionName ) in file.functions"
         v-bind:key="functionName"
         >
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
                <tr v-for="(violation) in violations" v-bind:key="violation.rule" :style="{backgroundColor: calculateBackgroundColor(violation.value/violation.threshold)}">
                  <td>{{violation.rule}}</td>
                  <td>{{violation.value}}</td>
                  <td>{{violation.threshold}}</td>
                </tr>
              </tbody>
            </table>
        </div></div>
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
      filterName: '',
      status: 'init',
      errors: [],
      details: {},
    }
  },
  computed: {
    filteredData: function() {
        return this.phpmdData.filter(item => {
          return item.name.includes(this.filterName)
        })
    },
  }, methods: { 
    calculateBackgroundColor: function(ratio) {
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
      self.status = 'analyzing'
      reader.onload = function(e) {
        let data
        try {
          data = JSON.parse(e.target.result)
          self.rawData = data
          const mapped = data.files.reduce((result, file) => {
          const name = file.file;
          let complexityRatio = 0
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
            complexityRatio += numbers[0] / numbers[1]
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
          complexityRatio = complexityRatio / file.violations.length
          result.push({name, functions, complexityRatio})
          return result
        }, [])
        self.phpmdData = mapped;
        self.status = 'display'
        } catch (err) {
          self.errors.push('Invalid File')
          }
      }
      reader.readAsText(file);
    },
    toggleDetails: function(name) {
      if(this.details[name]) {
        this.details[name] = false
      } else {
        this.details[name] = true
      }
      this.details = { ...this.details } // immutablity
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
