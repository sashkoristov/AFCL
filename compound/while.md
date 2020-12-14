# While

The `while` compound function is used to execute a `loopBody` zero or more times, depending on the specified `condition`. The `condition` has the same structure as in the `if-then-else` compound function. The `loopBody` will be executed until the specified `condition` evaluates to `false`. Similarly as in the `for` compound function, dependencies across loop iterations in `while` can be expressed with `dataLoop` ports.

````yaml
while: {
  name: "name",
  dataIns: [{}+]?,
  dataLoops: [
    {
      name: "name",
      type: "type",
      initSource: "source",?
      loopSource: "source",
      value: "constant"?
    }+
  ]?,
  condition: 
    {
      combinedWith: "and/or",
      conditions: [{}+]
    },
  loopBody: [{function: {}}+],
  dataOuts: [{}+]?
}
````