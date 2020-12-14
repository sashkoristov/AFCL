# Sequence


The `sequence` compound function represents a sequential control-flow of all inner functions within the `sequenceBody` section. In order to simplify *AFCL*, we assume that all base or compounds functions, which are specified one after the other, are sequential without specifying them into a `sequence` compound function. *AFCL* introduces `source` attribute for `dataOuts` ports for each compound function in order to specify internal data-flow from `dataOuts` ports of children functions to `dataOuts` ports of the parent compound function. The value of the `source` attribute is similar to that of the `source` attribute of a `dataIns` port, except that it refers to `dataOut` ports of children functions of a compound function.


````yaml
sequence: {
  name: "name",
  dataIns: [{}+]?,
  sequenceBody: [
    {
      function: {}+
    }
  ],
  dataOuts: [
    {
      name: "name",
      type: "type",
      saveto: "saveto"?
      source: "source",
    }+
  ]?
}
````