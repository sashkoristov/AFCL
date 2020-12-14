# AFCL - Abstract Function Choreography Language

This project presents a novel language at a high-level of abstraction to build serverless workflows applications (Function Choreographies - *FCs*). 

## General overview of AFCL

AFCL is based on YAML. An AFCL FC consists of functions, which can be either base functions or compound functions. The former refers to a single computational task without further splitting it into smaller tasks, while the latter encloses some base functions or even nested compound functions. All base and compound functions can be connected by different control- and data-flow constructs. An FC is also a compound function. In order to create an FC, all its functions (base and compound) as well as control- and data- flow connections among them, must be specified. In order to facilitate optimized execution of FCs, a user optionally can specify properties and constraints for functions and data-flow connections. 

In order to simplify the reading of AFCL specifications, we use meta-syntax which extends YAML, such that YAML elements can be contained in { } and appended with wildcards “?” (0 or 1), “*” (0 or more), “+” (1 or more), and “|” (logical or).


## Base functions

A base function represents a computational or data processing task. The name of a function serves as a unique identifier. Functions are described by type - *Function Types* (FTs) which are abstract descriptions of their corresponding *function implementations* (FIs). An FI represents an actual implementation of an FT. 
The FIs of the same function type are semantically equivalent, but may expose different performance or cost behavior or may be implemented with different programming languages, etc. FTs shield implementation details from the FC developer. The selection of a specific FI for an FT is done by an underlying runtime system.

Finally, a single FI may be deployed on mulitple regions of a single FaaS system or assigned with different RAM memory. We are using the term *function deployment* (*FD*) for each deployment of a single function implementations. 

This means that one FT may have multiple FIs, each of which may have multiple FDs.


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

AFCL introduces the following compound functions: `sequence`, `if-then-else`, `switch`, `for`, `while`, `parallel`, and `parallelFor`. The specifications for the name attribute, dataIns and dataOuts ports, along with the corresponding source and saveto attributes are similar as for a base function. 

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

More details will follow very soon ...
Still, details regarding AFCL can be found in the following publiscations.

----

# Publications



@article{ristov114afcl,
  title={AFCL: An Abstract Function Choreography Language for serverless workflow specification},
  author={Ristov, Sasko and Pedratscher, Stefan and Fahringer, Thomas},
  journal={Future Generation Computer Systems},
  volume={114},
  pages={368--382},
  publisher={Elsevier},
  doi={https://doi.org/10.1016/j.future.2020.08.012}
}