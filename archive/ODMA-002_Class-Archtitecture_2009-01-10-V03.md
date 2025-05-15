|                           |                          |
|---------------------------|-------------------------:|
| xaldon Technologies GmbH. |   TECH-DOC: **ODMA-002** |
| Editor: Stefan Kopf       |              Version 0.3 |
|                           | Release date: 01/10/2009 |

# OpenDMA – Class architecture

## Preface

The Open document management architecture (OpenDMA) is based on an *abstract* architecture that is able to cover all existing document management systems. This abstract architecture is defined in multiple layers (*sections* in this document), where each layer is able to use the features offered by a lower layer.

## Section I: Object oriented OpenDMA model

OpenDMA uses a strongly typed reflective object oriented data model with a class hierarchy. Section I.1 first defines a *simple object model* that has as less constraints as possible. Section I.2 then adds the constraints and reflection needed by OpenDMA.

### Section I.1: Simple object model

This first subsection defines an object model without any constraints. Those objects are often referred to as *soft objects*.

#### §1 Qualified names

The OpenDMA architecture uses only qualified names. Each consists of

1.  a qualifier (Unicode text string with 1 or more characters; may be null), and
2.  a name (Unicode text string with 1 or more characters; may not be null),

where the qualifier can be omitted (null).

The qualifier “opendma.org” is reserved for this architecture specification.

#### §2 Data types

The simple object model is able to hold data as scalar values or un-typed references to other objects. Both may be either single valued or multiple valued (arrays).

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
9.  BLOB (Binary large object)

##### §2.2 Reference values

The simple object model can reference other objects in the same context (see §3) by holding their unique identifier (see §3).

##### §2.3 Content

The OpenDMA class architecture knows the special data type “Content” that is not usual to other well known object models. This data type stores octet streams and is accessed in a stream typed manner.

##### §2.4 Cardinality

Each data type (scalar or reference) can hold either exactly one (single value) or a list of zero or more (multi value) elements of the same data type.

##### §2.5 Date type id

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
| BLOB (§2.1)          | 9                |
| Reference (§2.2)     | 10               |
| Content (§2.3)       | 11               |

##### §2.4 Nullability

The OpenDMA data model knows the special value null as representation for “not assigned”.

#### §3 Objects

An object is a list of zero or more properties (see §4). Each object can be uniquely identified in its *context* by some *unique object identifier* that must be presentable as String.

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

Every object must have at least a single valued property (§4) with the qualified name \[“opendma.org”, “Class”\]. This property is a reference property (§2.2) that has to contain a reference to a *valid class object* (§8.3). The value must not be null.

The properties of every object must exactly match in number, data type, cardinality and nullability the *effective property list* (§10) defined by the valid class object.

#### §7 Class info object

A *class info object* is an object with at least theses properties:

1.  \[“opendma.org”, “Class”\], single value, Reference, as defined in §6
2.  \[“opendma.org”, “Name”\], single value, String
3.  \[“opendma.org”, “NameQualifier”\], single value, String
4.  \[“opendma.org”, “Parent”\], single value, Reference to a valid class object as defined in §8.3
5.  \[“opendma.org”, “Aspects”\], multi value, Reference to valid aspect objects as defined in §8.4
6.  \[“opendma.org”, “DisplayName”\], single value, String
7.  \[“opendma.org”, “Properties”\], multi value, Reference to property info objects as defined in §9

These constraints apply to the properties:

- All properties except “Aspects” and “Parent” must not be null. “Properties” still may contain 0 elements.

- The restrictions of “Parent” are defined in §8.

- All property info objects (§9) referenced in the “Properties” property must be unique in the list of effective properties (§10) of this class regarding the tuple (”NameQualifier”,”Name”). This implies that (a) no qualified property name may be used twice in this list and (b) no qualified property name already used by a parent class or an aspect class may be reused. Properties can not be overwritten.

- The tuple (”NameQualifier”,”Name”) must be unique in the class hierarchy (§8.3)

#### §8 Class hierarchy

##### §8.1 Class hierarchy root

Every context (§3) contains at least one class info object (§7) with these property values:

| **Property name**                  | **Value**                                                                                                                                     |
|:-----------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------|
| \[“opendam.org”, “Class”\]         | Reference to the class class object (§8.2)                                                                                                    |
| \[“opendam.org”, “Name”\]          | String “Object”                                                                                                                               |
| \[“opendam.org”, “NameQualifier”\] | String “opendma.org”                                                                                                                          |
| \[“opendma.org”, “Parent”\]        | NULL                                                                                                                                          |
| \[“opendma.org”, “Aspects”\]       | NULL                                                                                                                                          |
| \[“opendam.org”, “DisplayName”\]   | String “OdmaObject”                                                                                                                           |
| \[“opendma.org”, “Properties”\]    | A 1 element multi value reference field containing a reference to an object as described in §9 describing the class property as defined in §7 |

This object is called the *class hierarchy root*.

##### §8.2 Class class object

Every context (§3) contains exactly one class info object (§7) with these property values:

| **Property name**                  | **Value**                                                                                                                                |
|:-----------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------|
| \[“opendam.org”, “Class”\]         | Reference to itself                                                                                                                      |
| \[“opendam.org”, “Name”\]          | String “Class”                                                                                                                           |
| \[“opendam.org”, “NameQualifier”\] | String “opendma.org”                                                                                                                     |
| \[“opendma.org”, “Parent”\]        | Reference to the class hierarchy root (§8.1)                                                                                             |
| \[“opendma.org”, “Aspects”\]       | NULL                                                                                                                                     |
| \[“opendam.org”, “DisplayName”\]   | String “OdmaClass”                                                                                                                       |
| \[“opendma.org”, “Properties”\]    | A 7 element multi value reference field containing references to 7 objects as described in §9 describing all properties as defined in §7 |

This object is called the *class class*.

##### §8.3 Valid class objects

A *valid class object* is a class info object (§7) following these conditions:

1)  The class hierarchy root (§8.1) is a valid class object.
2)  All class objects containing a reference to the class hierarchy root (§8.1) in their parent property are valid class objects.
3)  All class objects containing a reference to a valid class object in their parent property are again valid class objects.

This forms a tree like structure called the *OpenDMA class hierarchy*.

##### §8.4 Valid aspect objects

A *valid aspect object* is a class info object (§7) that is not a valid class object (§8.3). This prevents multi inheritance.

#### §9 Property info object

A *property info object* is an object with at least theses properties:

1.  \[“opendma.org”, “Class”\], single value, Reference, as defined in §6
2.  \[“opendma.org”, “Name”\], single value, String
3.  \[“opendma.org”, “NameQualifier”\], single value, String
4.  \[“opendma.org”, “DataType”\], single value, Integer
5.  \[“opendma.org”, “ReferenceClass”\], single value, Reference
6.  \[“opendma.org”, “MultiValue”\], single value, Boolean
7.  \[“opendma.org”, “Required”\], single value, Boolean
8.  \[“opendma.org”, “DisplayName”\], single value, String

These constraints apply to the properties:

- “Class”, “Name”, “NameQualifier”, “DataType”, “MultiValue”, “Required” and “DisplayName” must not be null.

- There exists exactly one class object (§7) in each context (§3) that describes property info objects. The “Class” property has to contain a reference to that class object.

- The value of “DataType” must be one of the list of numeric data type ids (§2.5).

- The value of “ReferenceClass” must contain a refence to a valid class object (§8.3) or a valid aspect object (§8.4) if and only if the value of “DataType” is 8. It must be null otherwise.

#### §10 Effective properties list

The *effective properties list* of a valid class object (§8.3) or valid aspect object (\$8.4) *c* is a list of property info objects (§9) defined as follows:

1)  all property info objects (§9) of *c*’s \[“opendma.org”, “Properties”\] Property are part of the effective properties list, and
2)  all property info objects (§9) of the effective properties list of the class object referenced by *c*’s \[“opendma.org”, “Parent”\] Property are part of the effective properties list.
3)  all property info objects (§9) of the effective properties list of the aspect objects referenced by *c*’s \[“opendma.org”, “Aspects”\] Property are part of the effective properties list.

#### §11 Failure messages

The object model knows a set of distinguished failure messages for the read / write operations (§5):

1.  ObjectNotFound
2.  InvalidDataType

##### §11.1 Property existence

The read and the write operation (§5) for a property *p* on an object *o* have to return an *ObjectNotFound* (§11) response code if and only if the effective property list (§10) of *o* does not contain a property info object for *p*.

##### §12.2 Type safety

The write operation (§5) for a property *p* on an object *o* has to return an *InvalidDataType* (§11) response code if and only if

1)  the effective property list (§10) of *o* does contain a property info object for *p*, and
2)  the value of \[“opendam.org”, “DataType”\] of that property info object does not match the data type of the value to be written.

The value returned by the read operation (§5) has to be of the data type defined by the numeric data type id read from the \[“opendam.org”, “DataType”\] Property of the corresponding property info for p.

## Section II: OpenDMA document management model

While the first section has defined a common object oriented model that could represent nearly everything, this second section defines a set of classes specific for document management.

This set of classes is divided into two parts, a set of basic document management classes and a set of extended document management classes. The first basic set is sufficient for all fundamental document management operations. On top of these adds the second set extended functionality that is not required in every environment.

### Section II.1: Basic document management model

The set of basic document management classes consists of:

- Repository  
  A Repository represents a place where Documents, Folders and Associations are stored.

- Document  
  A Document represents an atomic content element that can be able to keep track of its changes (versions) and manage the access to it (checkin and checkout).

- Folder  
  A Folder holds a list of Document, Folder or other objects that are said to be contained in this Folder. This list is build up with Associations.

- Association  
  An Association represents the directed or undirected link between two objects.

#### §13 Repository class

The \[“opendma.org”,”Repository”\] class extends the \[“opendma.org”,”Object”\] class and has these additional properties:

| **Property** | **Type**      | **Contents**                 |
|:-------------|:--------------|:-----------------------------|
| Title        | String (s,nn) | The title of this repository |
|              |               |                              |

#### §14 Document class

The (“opendma.org”,”Document”) class extends the (“opendma.org”,”Object”) class and has these additional properties:

| **Property**   | **Type**     | **Contents**                                                                                     |
|:---------------|:-------------|:-------------------------------------------------------------------------------------------------|
| Title          | String (s)   | The title of this document                                                                       |
| Version        | String (s)   | Identifier of this version consisting of a set of numbers separated by a point (e.g. 1.2.3)      |
| Content        | Content (s)  | The content of this document                                                                     |
| CheckedOut     | Boolean (s)  | Whether this document is checked out or not.                                                     |
| Size           | Long (s)     | The size of the content in octets                                                                |
| MimeType       | String (s)   | The mime type of the content                                                                     |
| CreatedAt      | DateTime (s) | When this document has been created                                                              |
| LastModifiedAt | DateTime (s) | When *this version* of the document has been created, i.e. when this document has been modified  |
| CheckedOutAt   | DateTime (s) | When this version of the document has been checked out; null if this document is not checked out |
| CreatedBy      | String (s)   | User who created this document                                                                   |
| LastModifiedBy | String (s)   | User who created this version of the document                                                    |
| CheckedOutBy   | Strng (s)    | User who checked out this version of the document; null if this document is not checked out      |
|                |              |                                                                                                  |

This definition of the Document class does not yet support multiple content elements. We need to extend this definition!

#### §15 Folder class

The \[“opendma.org”,”Folder”\] class extends the \[“opendma.org”,”Object”\] class and has these additional properties:

| **Property** | **Type**      | **Contents**                              |
|:-------------|:--------------|:------------------------------------------|
| Title        | String (s)    | The title of this repository              |
| Containees   | Reference (m) | The objects contained in this folder      |
| Associations | Reference (m) | The associations of the contained objects |
|              |               |                                           |

#### §16 Association class

The \[“opendma.org”,”Association”\] class extends the \[“opendma.org”,”Object”\] class and has these additional properties:

| **Property** | **Type**      | **Contents**                                 |
|:-------------|:--------------|:---------------------------------------------|
| Head         | Reference (s) | The source of this directed association      |
| Tail         | Reference (s) | The destination of this directed association |
| Name         | String (s)    | The name of this association                 |
|              |               |                                              |

### Section II.2: Extended document management model

The set of extended document management classes consists of:
