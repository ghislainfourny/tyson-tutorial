# TYSON Tutorial

This tutorial gives a user-friendly introduction to the TYSON syntax.

## Executive summary

TYSON extends JSON with type support, and that's all it does.

It is backward compatible with JSON.

It is designed to be compatible with any JSON Schema technology, meaning that it can be used as input and annotated output of such technologies with little work.

It is compatible with any host language and is fully agnostic in this respect.

## JSON is TYSON

The first important thing to know is that any JSON value is also a TYSON value.

This includes strings:

```
"foo"
```

Numbers:

```
   1
   3.14
   6.022e23
```

Booleans:

```
   true
   false
```

Null:

```
   null
```

Arrays:

```
   [ "foo", 1, null, true, [ 1, 2, 3, 4 ] ]
```

And objects:

```
   {
     "foo" : "bar",
     "bar" : {
       "foo" : [ 1.2, 2, 4, 5, 2e5 ],
       "bar" : true,
       "foobar" : null
   }
```

## More atomic types

What is new with TYSON is type support beyond the basic types of JSON. Anybody can create types, give them a name, and go ahead and use them (how exactly these types are created, documented and agreed on is out of scope of TYSON, though).

For example, in a straightforward way, atomic types from XML Schema can be taken over with no effort:

For example, you can have dates:

```
   ("date") "2018-09-01" 
```

Binary values:

```
   ("hexBinary") "0123456789abcdef"
```

Types such as the above are not part of the TYSON specification itself, but they are introduced in JSound, a schema language that works with TYSON.

Object and array types can also be user-defined:

```
   ("array-of-booleans") [ true, true, false, true, false ]
```

```
   ("person") {
     "name" : "Cooper",
     "first" : "Sheldon",
     "birthdate" : ("date") "1980-02-26",
     "picture" : ("hexBinary") "0123456789abcdef",
     "friends" : ("ids") [ 1, 2, 4, 5 ]
   }
```

A type annotation can be put in front of absolutely any value, as a string in parenthesis. And this is all there is to know about the TYSON syntax in itself, because there is nothing else.

# Builtin types

TYSON explicitly introduces builtin types corresponding to JSON values:

- object (all JSON objects)
- array (all JSON arrays)
- string (all JSON strings, sequences of Unicode codepoints)
- boolean (`true` and `false`)
- integer (all integers, with no limit or bound)
- decimal (all decimal numbers, with no limit or bound)
- double (all IEEE754 doubles)
- null (a type that only matches the value `null`)

For backward compatibility and ease of use, these annotations are implicit. The document seen above is semantically the same as the more explicit:

```
   ("object") {
     "foo" : ("string") "bar",
     "bar" : ("object") {
       "foo" : ("array") [ ("decimal") 1.2, ("integer") 2, ("integer") 4, ("integer") 5, ("double")2e5 ],
       "bar" : ("boolean") true,
       "foobar" : ("null") null
   }
```

For numbers, anything with no dots (like `1`) and no scientific notation is implicitly an integer, anything with a dot and no scientific notation (like `1.1`) a decimal, anything in scientific notation (like `1e1`) a double.

TYSON does not introduce any other type (not even date or hexBinary, which is left to schema technologies such as JSound).

# Builtin type validation

TYSON performs validation against the above builtin types, and only them.

For example, the following values are not valid TYSON:

```
("string") {}
("array") "foo"
("boolean") "bar"
```

TYSON, however, does not know of any other types, and validation must be done separately. For example, the following value is comformant TYSON, even though it is to be expected that some machinery in the outside world would say it is invalid:

```
("my-integer") "1.1"
```

# Number types

Thanks to the more fine-grained number types, it is possible to have more control about how some data appears to a host language, beyond just calling it a number:

```
("decimal") 2
```

```
("double") 3.14
```

```
("integer") 1e10
```

# Define your own types

TYSON poses very little restrictions on types. All it requires is:

- for an object type to be associated with a value space (a subset of the set of all objects),
- for an array type to be associated with a value space (a subset of the set of all arrays),
- for an atomic type to be associated with a value space, a lexical space (the string representations of the values) and a lexical mapping that associates each lexical value with a typed value (the mapping may not be injective).

The details of creating a type, documenting it, sharing it with the world, agreeing on names, type discovery and so on is out of scope of TYSON. All TYSON does is provide syntax to losslessly store and share typed data once types are agreed on.

# Quotes

For the purpose of validation, TYSON does not differentiate between quoted and unquoted values: it takes whatever there is as the lexical value, excluding the quotes if there are any.

The following two values are the same: the integer 1.
```
("integer") 1
("integer") "1"
```

Note that this has nothing to do with casting. TYSON does not cast. It only looks whether the lexical value is in the lexical space of an atomic type.

This means that value that JSON allows to be unquoted do not need quotes, and can be used as lexical values of any atomic type.

# Specification

For more information, or to know more on corner cases, the TYSON specification can be found at [www.tyson-spec.com](www.tyson-spec.com)
