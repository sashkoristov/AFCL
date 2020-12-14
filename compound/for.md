# For

The `for` compound function executes its `loopBody` multiple times based on the specified `loopCounter`. The value of the `loopCounter` is initially set to the value specified by the attribute `from` and is then increased by the value of step until it reaches the `value` of to or larger. The attributes `from`, `to`, and `step` can be specified with a constant value or with data ports of other functions. To express dependencies across loop iterations the `dataLoop` ports are used. These ports get their initial value from the optional `initSource` field or a constant value from the value field. A `loopSource` field specifies a data-flow from the output of a function of the `loopbody` which can be used as input to functions executed in the next loop iteration. `name` is an unique identifier of a DataLoop port and type specifies the data type of the value.

````yaml
for: {
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
  loopCounter: 
    {
      name: "name",
      type: "type",
      from: "from",
      to: "to",
      step: "step"?,
    },
  loopBody: [{function: {}}+],
  dataOuts: [{}+]?
}
````