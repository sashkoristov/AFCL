# Ivocation type for a base function

The `invoke-type` property can be used to specify whether a base function should run asynchronously (`ASYNC`) or synchronously (`SYNC`). An invoker of a synchronous function waits for the function to finish (if the function has output data then until the data arrived). With asynchronous invocation, the function will be queued without waiting for the function to be finished. For example, the runtime system can invoke a synchronous ”checker” function periodically to examine whether the output of an asynchronous function is stored in a specified storage (e.g. S3). 

````yaml
function: {
  name: "name", type: "type",
  dataIns: [{}+]?,
  properties: [
    {
      name: "invoke-type",
      value: "ASYNC | SYNC"
    }+
  ]?,
  dataOuts: [{}+ ]?
}
````