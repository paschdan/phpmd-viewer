<template>
  <div>
    <div v-if="status == 'init'">
      <h2>Upload a File phpmd json file</h2>
      <input type="file" @change="processFile($event)" accept=".json, .JSON" />
    </div>
    <div v-if="status == 'analyzing'">
      Analizing the file...
    </div>
  </div>
</template>

<script>
export default {
  name: "Uploader",
  data: function() {
    return {
      status: "init"
    };
  },
  methods: {
    processFile: function(event) {
      let file = event.target.files[0];
      const reader = new FileReader();
      const self = this;
      self.status = "analyzing";
      reader.onload = function(e) {
        let data;
        try {
          data = JSON.parse(e.target.result);
          self.rawData = data;
          const mapped = data.files.reduce((result, file) => {
            const name = file.file;
            let complexityRatio = 0;
            const functions = {};
            file.violations.forEach(violation => {
              if (violation.function && !functions[violation.function]) {
                functions[violation.function] = [];
              }
              if (violation.method && !functions[violation.method]) {
                functions[violation.method] = [];
              }
              if (
                violation.class &&
                !violation.method &&
                !functions[violation.class]
              ) {
                functions[`Class_${violation.class}`] = [];
              }
              const numbers = violation.description.match(
                /^\d+|\d+\b|\d+(?=\w)/g
              );
              complexityRatio += numbers[0] / numbers[1];
              const violationObject = {
                rule: violation.rule,
                value: numbers[0],
                threshold: numbers[1]
              };
              if (violation.function) {
                functions[violation.function].push(violationObject);
              }
              if (violation.method) {
                functions[violation.method].push(violationObject);
              }
              if (violation.class && !violation.method) {
                functions[`Class_${violation.class}`].push(violationObject);
              }
            });
            complexityRatio = complexityRatio / file.violations.length;
            result.push({ name, functions, complexityRatio });
            return result;
          }, []);
          self.$emit("analyzedData", mapped);
        } catch (err) {
          self.$emit("Error", "Invalid File");
        }
        self.status = "init";
      };
      reader.readAsText(file);
    }
  }
};
</script>