# fungen [![Build Status](https://api.travis-ci.org/kulshekhar/fungen.svg?branch=master)](https://travis-ci.org/kulshekhar/fungen)


fungen is a tool that can be used with `go generate` to automate the creation of list like types and methods like Map, Filter, Reduce, etc on these types.

This allows you to use constructs like the following on lists of any types:

```go

myList.Map(func(t type1) type1 {

...

}).Filter(func(t type1) bool {

...

}).Take(3).Reduce(initialValue type1, func (prev type1, current type1) type1 {

...

})

```

The `Map` and `Filter` methods have parallel counterparts `PMap` and `PFilter` which can be used to parallelize time consuming operations.

__Note:__ This tool uses standard golang constructs to make this functionality available on any type - standard and custom. Interfaces have NOT been used.

## Motivation

While the `for` loop in Go is quite nice, it still involves some boilerplate code. In my particular case, I realized that I could eliminate a majority of that code by using the following constructs:

- __Map__ (apply a function to each member of a list and return the resulting list - of either the same type or a different type)
- __PMap__ (parallel map)
- __Filter__ (apply a function to each member of a list to retrieve only the ones that satisfy some criteria)
- __PFilter__ (parallel filter)
- __FilterMap__ (applies the filter(s) and map to the list members in a single loop and returns the resulting list containing members of the mapped type)
- __PFilterMap__ (parallel FilterMap)
- __Reduce__ (perform aggregation functions on a list)
- __ReduceRight__
- __Each__ (execute any function on each element of a list)
- __EachI__
- __Take__ (create a new list containing only the first n elements of another list)
- __TakeWhile__ (take the first elements that satisfy a particular criteria)
- __Drop__ (create a new list by excluding the first n elements of another list)
- __DropWhile__ (exclude the first elements that satisfy a particular criteria)
- __All__ (returns true if all the members of the list satisfy a function or if the list is empty)
- __Any__ (returns true if at least one member of the list satisfies a function)

## How to Use

Assuming you have installed `fungen` (see below), you can start by including the comment in your file for `go generate` to process. Let's assume we want to use the above methods with a list of strings (type []string).

Add the following comment in your source code:

```go
//go:generate fungen -types string
```

Now run the `go generate` command in the same directory where this source file is located. This will generate a file named `fungen_auto.go` in this directory. This file will contain the type and method definitions that you'll need.

If you take a look at the file, you'll see that a new type has been created:

```go
type stringList []string
```

If you now use this type (`stringList`) in place of `[]string`, you'll be able to use all the above mentioned methods on that list of string.

```go

words := stringList{"calculator", "skip", "grape", "obtainable", "inexpensive", "panoramic", "powerful"}

wordsWithLengthLessThan7 := words.Filter(func(w string) bool {
  return len(w) < 7  
})
// wordsWithLengthLessThan7 = [skip grape]

```

## Installation

```
go install github.com/kulshekhar/fungen
```

## Code Generation
The complete command to generate the code:

```
fungen -package PackageName -types comma,Separated,Types,With,Optional:Opt,short:Sh,names:n
```

### Use from source code:

```go
//go:generate fungen -types string,int
```
followed by
```
go generate
```
on the command line, in the directory where you wish the generated file to be stored.


### Use From the command line:

```
fungen -types string,int
```

This tool will generate as a file named `fungen_auto.go`.

## Explanation of Options

```
-package PackageName
```

The `-package` parameter is optional. If specified, it will generate the code file by using `package PackageName` at the top. If omitted, it will generate the code file by using `package main` at the top. 

```
-types comma,Separated,Types,With,Optional:Opt,short:Sh,names:n
```

The `-types` parameter takes a comma separated list of types for which the list type and methods on this type should be generated. eg: (int,string,uint,customType)

Each of the comma separated values can themselves optionally be a colon separated value. If this is the case, the first part (before the colon) should be a valid type name (built in or custom) and the second is the name used in the names of the methods.

```
-filename filename.go
```

Filename for generated package (default "fungen_auto.go"). The `-filename` parameter is optional.

```
-methods Map,Filter
```

Comma separated list of methods to generate. By default generate all methods.

Valid methods is: Map,PMap,Filter,PFilter,Reduce,ReduceRight,Each,EachI,Take,TakeWhile,Drop,DropWhile,All,Any,FilterMap,PFilterMap

#### Example 1

If `-types int,string` is used, the types generated will be:

```go
type intList []int
type stringList []string

```

The following methods will be available on both these types:

```
Map
PMap
Filter
PFilter
Reduce
ReduceRight
Take
TakeWhile
Drop
DropWhile
Each
EachI
All
Any

```

Additionally, the `intList` type will have the following methods:

```
MapString
PMapString
FilterMapString
PFilterMapString
```

And the `stringList` type will have the following methods:

```
MapInt
PMapInt
FilterMapInt
PFilterMapInt
```

#### Example 2

If `-types int:I,string:Str` is used, the names of the common set of methods generated will be the same as above. However, the types and the additional methods generated will have different names.

The types will be named as follows:

```go
type IList []int
type StrList []string

```


The `IList` type will have the following methods:

```
MapStr
PMapStr
```

And the `StrList` type will have the following methods:

```
MapI
PMapI
```

#### Feedback, critique and contributions are all welcome.
