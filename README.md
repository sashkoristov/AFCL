# AFCL - Abstract Function Choreography Language

This project presents a novel language at a high-level of abstraction to build serverless workflows applications (Function Choreographies - *FCs*). 

*AFCL* is the language that each component of the overall [AFCL Environment](https://github.com/sashkoristov/AFCLEnvironment) uses and understands.

You can build an FC using:
* Any YAML-based editor 
* [FC Editor](http://fceditor.dps.uibk.ac.at:8180/)
* [AFCLCore](./AFCLCore/afclCore.jar) in JAVA program


## General overview of AFCL

AFCL is based on YAML. An AFCL FC consists of functions, which can be either base functions or compound functions. The former refers to a single computational task without further splitting it into smaller tasks, while the latter encloses some base functions or even nested compound functions. All base and compound functions can be connected by different control- and data-flow constructs. An FC is also a compound function. In order to create an FC, all its functions (base and compound) as well as control- and data- flow connections among them, must be specified. In order to facilitate optimized execution of FCs, a user optionally can specify properties and constraints for functions and data-flow connections. 

In order to simplify the reading of AFCL specifications, we use meta-syntax which extends YAML, such that YAML elements can be contained in { } and appended with wildcards “?” (0 or 1), “*” (0 or more), “+” (1 or more), and “|” (logical or).


## Base functions

A base function represents a computational or data processing task. The name of a function serves as a unique identifier. Functions are described by type - *Function Types* (FTs) which are abstract descriptions of their corresponding *function implementations* (FIs). An FI represents an actual implementation of an FT. 
The FIs of the same function type are semantically equivalent, but may expose different performance or cost behavior or may be implemented with different programming languages, etc. FTs shield implementation details from the FC developer. 

Finally, a single FI may be deployed on mulitple regions of a single FaaS system or assigned with different RAM memory. We are using the term *function deployment* (*FD*) for each deployment of a single function implementation. 

This means that one FT may have multiple FIs, each of which may have multiple FDs.

The selection of a specific FD for an FT is done by an underlying runtime system *xAFLC*.


````yaml
function: {
  name: "name",
  type: "type",
  dataIns: [ 
    {
      name: "name",
      type: "type",
      source: "source"?,
      value: "value"?
    }+
  ]?,
  dataOuts: [
    {
      name: "name",
      type: "type",
      saveto: "saveto"?
    }+
  ]?
}

````

The input and output data of a function are specified through dataIns and dataOuts ports of the function, respectively. The number and type attributes of the dataIns/Outs ports are uniquely determined by the chosen FT. A dataIns port can be specified by 
* setting its source attributed to the name of a data port of another function within the same FC (dataIns from a parent compound function or dataOuts from a predecessor function) or the name of a dataIns port of the FC; 
* setting its source attribute to a specific URL referring to a file, or an ordered list of URLs referring to an ordered list of files; or 
* specifying a constant or an ordered list of constants. 

The data associated with the dataOuts port can be stored in the location specified through its saveto attribute. Linking the data ports of different functions through the source attributes defines the data-flow of AFCL FCs. The value of a dataIns/Outs port can be used to define a constant. dataIns/Outs ports are optional as a function may perform some predefined action in which case there is no need to specify neither input nor output parameters. Every dataIns/Outs port is associated with a data type. The data types supported for AFCL are JSON datatypes: string, number, boolean, null/empty, object, and array, as well as two additional types, file and collection.

We omit name, type, source, value, and saveto in definitions for simplicity. Instead, we will use only expressions dataIns[{}+]? and dataOuts[{}+]? for the list of data inputs and outputs of a function. Additionally, we will use the abbreviation function[{}+] to specify a list of base or compound functions (omitting name, type, dataIns, and dataOuts).


## Compound functions

AFCL introduces a rich set of control-flow constructs (compound functions) to simplify the specification of realistic FCs that are difficult to be composed with any current FC system without support by a skilled software developer. Compound functions contain inner functions, which can be base or compound and they are executed in the order defined by the compound function. An inner function of a compound function fi can be another compound or base function fj . 

AFCL introduces the following compound functions: [`sequence`](./compound/sequence.md), [`if-then-else`](./compound/if.md), [`switch`](./compound/switch.md), [`for`](./compound/for.md), [`while`](./compound/while.md), [`parallel`](./compound/parallel.md), and [`parallelFor`](./compound/parallelFor.md). The specifications for the name attribute, dataIns and dataOuts ports, along with the corresponding source and saveto attributes are similar as for a base function. 

When we use the term function, it can refer to either base function or compound function.


## Properties and constraints

Properties and constraints are optional attributes defined within the AFCL language, which provide additional information about dataIn ports, dataOut ports, and base and compound functions. 

Properties can be used to describe hints about the behaviour of functions, e.g. expected size of input data or memory required for execution. 

Constraints (e.g. finish execution time within a time limit, data distributions, fault tolerance settings) should be fulfilled by the runtime system on a best-effort basis.

````yaml
function: {
  name: "name",
  type: "type",
  dataIns: [
    {
      name: "name", type: "type",
      source: "source"?, value: "value"?,
      properties: [{ name: "name", value: "value" }+]?,
      constraints: [{ name: "name", value: "value" }+]?
    }+
  ]?,
  properties: [{ name: "name", value: "value" }+]?,
  constraints: [{ name: "name", value: "value" }+]?,
  dataOuts: [
    {
      name: "name", type: "type", saveto: "saveto"?,
      properties: [{ name: "name", value: "value" }+]?,
      constraints: [{ name: "name", value: "value" }+]?
    }+
  ]?
}
````

## Invocation type

The `invoke-type` is a built-in `Property` defined in *AFCL*. This `Property` can be used to specify whether a base function should run asynchronously (ASYNC) or synchronously (SYNC). An invoker of a synchronous function waits for the function to finish (if the function has output data then until the data arrived). With asynchronous invocation, the function will be queued without waiting for the function to be finished. For example, the runtime system can invoke a synchronous ”checker” function periodically to examine whether the output of an asynchronous function is stored in a specified storage (e.g. S3). By default, if the invoke-type property is defined within a compound function, all nested base functions within that compound function will inherit this property and are invoked with the specified invoke-type. Otherwise, the base function will be invoked as specified invoke-type property. If ASYNC is specified, the FC designer must guarantee that the FC still operates correctly. Without specifying any invoke-type in any of the parent compound functions, the base functions are executed synchronously in AFCL. The build-in function asyncHandler is used to handle ASYNC invoked functions. This function can be used the same way as a base function is used, while the type field specifies that it is a build-in function. The build-in function has one input parameter, representing a coma separated list of names of ASYNC invoked functions (e.g. FunctionName1) and one boolean output parameter which represents whether all of these invoked functions finished. asyncHandler can be invoked with invoke-type i) ASYNC, meaning that asyncHandler immediately returns with the output parameter set to true if all functions (specified in the input parameter) finished otherwise it is set to false, or ii) SYNC, meaning that asyncHandler waits for all functions (specified in the input parameter) to finish before it returns.

## Base function

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


## Cmpound function

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


# More details 

More details will follow very soon ...

Still, details regarding *AFCL* can be found in the following publications.

----

# Publications


````
@article{ristovAFCL:2020,

  title={AFCL: An Abstract Function Choreography Language for serverless workflow specification},

  author={Ristov, Sasko and Pedratscher, Stefan and Fahringer, Thomas},

  journal={Future Generation Computer Systems},

  volume={114},

  pages={368--382},

  publisher={Elsevier},

  doi={https://doi.org/10.1016/j.future.2020.08.012}

}
````

# Support

If you need any additional information, please do not hesitate to contact sashko@dps.uibk.ac.at.

