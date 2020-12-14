# Parallel

The `parallel` compound function expresses the parallel execution of a set of sections. Each `section` within the `parallelBody` represents a list of base or a compound functions, which can run in parallel with other sections. The `parallel` compound function can have arbitrary many data input ports, whose associated data can be distributed among inner functions.

````yaml
parallel: {
  name: "name",
  dataIns: [{}+]?,
  parallelBody: [
    {
      section: [{function: {}}+]
    }+
  ],
  dataOuts: [{}+]?
}
````