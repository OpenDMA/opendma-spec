|                           |                          |
|---------------------------|-------------------------:|
| xaldon Technologies GmbH. |   TECH-DOC: **ODMA-002** |
| Editor: Stefan Kopf       |              Version 0.1 |
|                           | Release date: 06/24/2008 |

# OpenDMA – Class architecture

## Preface

The Open document management architecture (OpenDMA) is based on an *abstract* architecture that is able to cover all existing document management systems. This abstract architecture is defined in multiple layers (*sections* in this document), where each layer is able to use the features offered by the layer below.

## Section I: Object oriented OpenDMA model

OpenDMA uses a strongly typed reflective object oriented model with a class hierarchy. Section I.1 first defines a *simple object model* with as less constraints as possible. Section I.2 then adds the constraints and reflection needed by OpenDMA.

### Section I.1: Simple object model

This first subsection defines an object model without any constraints.

#### §1 Qualified names

The OpenDMA architecture uses only qualified names. Each consists of

1.  a qualifier (Unicode text string with 1 or more characters; may be null), and
2.  a name (Unicode text string with 1 or more characters; may not be null),

where the qualifier can be omitted (null).

The qualifier “opendma.org” is reserved for this architecture specification.

#### §2 Data types

The simple object model is able to hold values as scalar data or un-typed references to other objects. Both may be either single valued or multiple valued.

##### §2.1 Scalar values

The OpenDMA class architecture knows these scalar data types:

1.  String
2.  Integer
3.  Short integer
4.  Long integer
5.  Float
6.  Double
7.  Boolean
8.  DateTime

##### §2.2 Reference values

The simple object model can reference other objects in the same context (see §3) by holding their unique identifier (see §3).

##### §2.3 Cardinality

Each data type (scalar or reference) can hold either exactly one (single value) or a list of zero or more (multi value) elements of the same data type.

##### §2.4 Date type id

A numeric *data type id* is assigned to each data type corresponding to this table:

| **Data type**        | **Data type id** |
|:---------------------|:-----------------|
| String (§2.1)        | 1                |
| Integer (§2.1)       | 2                |
| Short integer (§2.1) | 3                |
| Long integer (§2.1)  | 4                |
| Float (§2.1)         | 5                |
| Double (§2.1)        | 6                |
| Boolean (§2.1)       | 7                |
| DateTime (§2.1)      | 8                |
| Reference (§2.2)     | 9                |

#### §3 Objects

An object is a list of zero or more properties (see §4). Each object can be uniquely identified in its *context* by some mathematical presentable *unique object identifier*.

Definition: *context*

A context is the place where the objects reside.

Definition: *unique object identifier*

A unique object identifier is some method to distinguish between objects in the same context.

Examples:

In a computer, the context would be the computer memory and the unique object identifier would be the address of the location where the object is stored in memory. If the machine supports different address spaces (like all modern computers do), each application has its own context, the application heap. The unique object identifier would by the relative address inside that heap.

In a database system, the context would be the table and the unique object identifier would be the record number.

#### §4 Properties

Each property consists of

1.  a qualified name as defined in §1,
2.  a data type as defined in §2, and
3.  a value depending on the data type or null as representation for “empty”.

#### §5 Operations on objects

Each object (§3) supports only these two operations:

- Read properties  
  input: name (§1)  
  output: value (§4) or null  
  Returns the value of a property identified by its name (§1)

- Write properties  
  input: name (§1), value (§4)  
  Modifies the value of a property identified by its name (§1)

Both operations may succeed or fail. This result (success or failure) has to be returned to the caller.

**Some notes on section I.1**

Please note:

- It is not defined, when the read or write operation has to succeed and when it has to fail.

- The outcome of the read and write operations is not defined. After writing a value for some property, it is not defined that a following read operation has to return this value or that it has to succeed at all.

### Section I.2: OpenDMA object model

The OpenDMA enforces some constraints on the simple object model for type safety and reflection.

#### §6 Reflection

Every object has a single valued property with the qualified name \[“opendma.org”, “Class”\]. This property is a reference property (§2.2) that has to contain a reference to a *valid class object* (§8.2). The value may not be null.

#### §7 Class object

A *class object* is an object with at least theses properties:

1.  \[“opendam.org”, “Class”\], single value, Reference, as defined in §6
2.  \[“opendam.org”, “Name”\], single value, String
3.  \[“opendam.org”, “NameQualifier”\], single value, String
4.  \[“opendma.org”, “Parent”\], single value, Reference to a valid class object as defined in §8.2
5.  \[“opendam.org”, “DisplayName”\], single value, String
6.  \[“opendma.org”, “Properties”\], multi value, Reference, as defined in §9

These constraints apply to the properties:

- “Class”, “Name” and “NameQualifier” may not be null (empty).

- The restrictions of “Parent” are defined in §8.

- All property info objects (§9) referenced in the “Properties” property must be unique in the list of effective properties (§10) of this class regarding the tuple (”NameQualifier”,”Name”). This implies that (a) no qualified property name may be used twice in this list and (b) no qualified property name already used by a parent class may be reused. Properties can not be overwritten.

#### §8 Class hierarchy

##### §8.1 Class hierarchy root

Every context (§3) contains at least one class object (§7) with these property values:

| **Property name**                  | **Value**                                                                                                                                |
|:-----------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------|
| \[“opendam.org”, “Class”\]         | Reference to itself                                                                                                                      |
| \[“opendam.org”, “Name”\]          | String “Class”                                                                                                                           |
| \[“opendam.org”, “NameQualifier”\] | String “opendma.org”                                                                                                                     |
| \[“opendma.org”, “Parent”\]        | NULL                                                                                                                                     |
| \[“opendam.org”, “DisplayName”\]   | String “OdmaClass”                                                                                                                       |
| \[“opendma.org”, “Properties”\]    | A 6 element multi value reference field containing references to 6 objects as described in §9 describing all properties as defined in §7 |

This object is called the *class hierarchy root*.

##### §8.2 Valid class objects

A *valid class object* is a class object (§7) following these conditions:

1)  The class hierarchy root (§8.1) is a valid class object.
2)  All class objects containing a reference to the class hierarchy root (§8.1) in their parent property are valid class objects.
3)  All class objects containing a reference to a valid class object in their parent property are again valid class objects.

This forms a tree like structure called the *OpenDMA class hierarchy*.

#### §9 Property info object

A *property info object* is an object with at least theses properties:

1.  \[“opendam.org”, “Class”\], single value, Reference, as defined in §6
2.  \[“opendam.org”, “Name”\], single value, String
3.  \[“opendam.org”, “NameQualifier”\], single value, String
4.  \[“opendam.org”, “DataType”\], single value, Integer
5.  \[“opendam.org”, “ReferenceClass”\], single value, Reference
6.  \[“opendam.org”, “MultiValue”\], single value, Boolean
7.  \[“opendam.org”, “Required”\], single value, Boolean
8.  \[“opendam.org”, “DisplayName”\], single value, String

These constraints apply to the properties:

- “Name”, “NameQualifier”, “DataType”, “MultiValue” and “Required” may not be null (empty).

- There exists exactly one class object (§7) in each context that describes property info objects. The “Class” property has to contain a reference to that class object.

- The value of “DataType” must be one of the list of numeric data type ids (§2.4).

- The value of “ReferenceClass” must contain a refence to a valid class object (§8.2) if and only if the value of “DataType” is 8. It must be null (empty) otherwise.

#### §10 Effective properties list

The *effective properties list* of a valid class object (§8.2) *c* is a list of property info objects (§9) defined as follows:

1)  all property info objects (§9) of *c*’s \[“opendma.org”, “Properties”\] Property are part of the effective properties list, and
2)  all property info objects (§9) of the effective properties list of the class object referenced by *c*’s \[“opendma.org”, “Parent”\] Property are part of the effective properties list.

#### §11 Failure messages

The object model knows a set of distinguished failure messages for the read / write operations (§5) :

1.  ObjectNotFoundException
2.  InvalidDataTypeException

#### §12 Restrictions on properties

This chapter uses the following definition to describe the constraints.

Definition: corresponding property info

The *corresponding property info* for some qualified name (§1) *p* of an object (§3) *o* is a property info object (§9) where

1)  the class object (§7) referenced by *o*’s \[“opendma.org”,“Parent”\] Property does contain a reference to this property info object, and
2)  the \[“opendma.org”,“Name”\] and \[“opendma.org”,“NameQualifier”\] Properties of this property info object match the qualified name *p*

##### §12.1 Property existence

The read and the write operation (§5) have to return an *ObjectNotFoundException* (§11) response code if and only if the corresponding property info for the requested qualified name does not exist for this object.

##### §12.2 Type safety

The write operation (§5) has to return an *InvalidDataTypeException* (§11) response code if and only if the value of the \[“opendam.org”, “DataType”\] Property of the corresponding property info for the requested qualified name of this object does not match the numeric data type id of the value of the operation.

The value returned by the read operation (§5) has to be of the data type defined by the numeric data type id read from the \[“opendam.org”, “DataType”\] Property of the corresponding property info for the requested qualified name of this object.
