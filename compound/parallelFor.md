# ParallelFor

The `parallelFor` compound function expresses the simultaneous execution of all loop iterations. It is assumed that there are no data dependencies across loop iterations. All other elements of the constructs behave the same as in the [`for`](for.md) compound function.

````yaml
parallelFor: {
  name: "name",
  dataIns: [{}+]?,
  loopCounter: 
    {
      name: "name", type: "type",
      from: "from", to: "to",
      step: "step"?,
    },
  loopBody: [{function: {}}+],
  dataOuts: [{}+]?
}
````