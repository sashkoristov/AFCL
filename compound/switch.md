# Switch

The `switch` compound function can be used to select a single case or a default compound function depending on the value of the `dataEval` attribute, thereby acting as an XOR logical expression. In case `break` is not used, then the `switch` compound function acts as an OR logical expression and multiple case branches might be selected.


````yaml
switch: {
  name: "name",
  dataIns: [{}+]?,
  dataEval: 
    {
      name: "name",
      type: "type",
      source: "source"?
    },
  cases: [
    {
      value: "value",
      break: "true"?,
      functions: [{function: {}}+]
    }+
  ],
  default: [{function: {}}+]?,
  dataOuts: [{}+]?
}
}
````