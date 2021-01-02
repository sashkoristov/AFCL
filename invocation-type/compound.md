#  Ivocation type for a compound function

By default, if the `invoke-type` property is defined within a compound function, all nested base functions within that compound function will inherit this property and are invoked with the specified invoke-type. Otherwise, the base function will be invoked as specified invoke-type property. If `ASYNC` is specified, the FC designer must guarantee that the FC still operates correctly. Without specifying any invoke-type in any of the parent compound functions, the base functions are executed synchronously in AFCL. The build-in function asyncHandler is used to handle `ASYNC` invoked functions. This function can be used the same way as a base function is used, while the type field specifies that it is a build-in function. The build-in function has one input parameter, representing a coma separated list of names of `ASYNC` invoked functions (e.g. `FunctionName1`) and one boolean output parameter which represents whether all of these invoked functions finished. `asyncHandler` can be invoked with `invoke-type` i) `ASYNC`, meaning that `asyncHandler` immediately returns with the output parameter set to true if all functions (specified in the input parameter) finished otherwise it is set to false, or ii) `SYNC`, meaning that `asyncHandler` waits for all functions (specified in the input parameter) to finish before it returns.

````yaml
function: {
  name: "name", type: "build-in:asyncHandler",
  dataIns: [
    { 
      name: "name", type: "collection",
      value: "FunctionName1,FunctionName2,...,FunctionNameN" 
	}+
  ],
  properties: [
    {
      name: "invoke-type",
      value: "ASYNC | SYNC"
    }+
  ]?,
  dataOuts: [
    { 
      name: "name", type: "boolean" 
	}+
  ]
}
````