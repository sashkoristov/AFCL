# if-then-else

The `if-then-else` compound function is one of two conditional compound functions of *AFCL*. The `condition` attribute describes a set of subconditions combined with the boolean `combinedWith` attribute. A `sub-condition` contains `data1` and `data2` which represent the data to be compared according to the value of the `operator` (==, <, >, ≤, ≥, and !=, `contains`, `startsWith` and `endsWith`) and optionally `negation`. The values of `data1` and `data2` can be constants or the output from previous functions. If the `condition` is satisfied then functions within the then part are executed, otherwise the `else` branch is executed.


````yaml
if: {
  name: "name",
  dataIns: [{}+]?,
  condition: 
    {
      combinedWith: "and/or",
      conditions: [
        {
          data1: "data1",
          data2: "data2",
          operator: "operator",
          negation: "negation",
        }+
      ]
    },
  then: [{function: {}}+],
  else: [{function: {}}+]?,
  dataOuts: [{}+]?
}
````