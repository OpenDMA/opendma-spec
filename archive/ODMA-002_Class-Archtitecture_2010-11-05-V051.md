|                           |                          |
|---------------------------|-------------------------:|
| xaldon Technologies GmbH. |   TECH-DOC: **ODMA-002** |
| Editor: Stefan Kopf       |            Version 0.5.1 |
|                           | Release date: 11/05/2010 |

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
2.  a name (Unicode text string with 1 or more characters; must not be null),

where the qualifier can be omitted (null).

The qualifier “opendma.org” is reserved for this architecture specification.

#### §2 Data types

The simple object model is able to hold data as scalar values or un-typed references to other objects. Both may be either single valued or multi valued (arrays).

##### §2.1 Scalar values

The OpenDMA class architecture knows these scalar data types:

1.  String (Unicode)
2.  Integer
3.  Short integer
4.  Long integer
5.  Float
6.  Double
7.  Boolean
8.  DateTime (including time zone. Time zone may be undetermined)
9.  BLOB (Binary large object)

##### §2.2 Reference values

The simple object model can reference other objects in the same context (see §4) by holding their unique identifier (see §4).

##### §2.3 Content

The OpenDMA class architecture knows the special data type “Content” that is not common in other well known object models. This data type stores very large octet streams and is accessed in a stream typed manner.

##### §2.4 Cardinality

Data of each data type can be either single valued or multi valued. Single valued data has exactly one value of the data type whereas multi valued data has a list of one or more elements of the same data type. An empty list of values is not allowed (see Nullability §2.4).

##### §2.5 Data type id

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

##### §2.6 Nullability

The OpenDMA data model knows the special value *null* as representation for “not assigned”. Multi valued data can either be null or not null in its entirety. An individual value of a multi valued data type must not be null.

<u>Info:</u> An empty multi valued filed is represented by the data being null instead of an empty list.

#### §3 Properties

A *property* is defined as a triple consisting of

1.  a qualified name as defined in §1,
2.  a data type as defined in §2, and
3.  a value depending on the data type or null as representation for “empty”.

#### §4 Objects

An *object* is a list of zero or more properties (see §3). Each object can be uniquely identified in its *context* by some *unique object identifier* that must be presentable as String.

Definition: *context*

A context is the place where the objects reside.

Definition: *unique object identifier*

A unique object identifier is some method to distinguish between objects in the same context.

Examples:

In a computer, the context would be the computer memory and the unique object identifier would be the address of the location where the object is stored in memory. If the machine supports different address spaces (like all modern computers do), each application has its own context, the application heap. The unique object identifier would by the relative address inside that heap.

In a database system, the context would be the table and the unique object identifier would be the record number.

#### §5 Operations on objects

Each object (§4) supports only these two operations:

- Read property
  input: qualified name (§1)
  output: property (§3) containing the value or null
  Returns the value of a property identified by its name.

- Write property
  input: qualified name (§1), value or null
  Modifies the value of a property identified by its name.

Both operations may succeed or fail. This result (success or failure) has to be returned to the caller.

**Some notes on section I.1**

Please note:

- It is not defined, when the read or write operation has to succeed and when it has to fail.

- The outcome of the read and write operations is not defined. After writing a value for some property, it is not defined that a following read operation has to return this value or that it has to succeed at all.

### Section I.2: OpenDMA object model

The OpenDMA enforces some constraints on the simple object model for type safety and reflection.

#### §6 Basic object constraints

The following constraints apply to all objects in the OpenDMA object model.

##### §6.1 Reflection

Every object must have at least a single valued property (§3) with the qualified name \[“opendma.org”, “Class”\]. This property is a reference property (§2.2) that has to contain a reference to a *valid class object* (§8.3). The property “IsInstantiable” of that valid class object must be true.

The properties of every object must exactly match in number, data type, cardinality and nullability the *effective property list* (§10) defined by this valid class object. A reference property *x* (§2.2) must only contain references to objects whose “Class” Property contains a reference to a class info object that is or extends (§8.4) the class info object referenced by the “ReferenceClass” property of *x*’s property info object (§9).

##### §6.2 Identification

Every object must have at least a single valued String property (§3) with the qualified name \[“opendma.org”, “Id”\]. This property contains the String representation of the unique object identifier as defined in §4.

#### §7 Class info object

A *class info object* is an object with at least theses properties:

1.  \[“opendma.org”, “Class”\], single value, Reference, as defined in §6.1, not null
2.  \[“opendma.org”, “Id”\], single value, String, as defined in §6.2, not null
3.  \[“opendma.org”, “Name”\], single value, String, not null
4.  \[“opendma.org”, “NameQualifier”\], single value, String, nullable
5.  \[“opendma.org”, “DisplayName”\], single value, String, not null
6.  \[“opendma.org”, “Parent”\], single value, Reference to a class info object (§7), nullable
7.  \[“opendma.org”, “Aspects”\], multi value, Reference to valid aspect objects (§8.4), nullable
8.  \[“opendma.org”, “DeclaredProperties”\], multi value, Reference to property info objects (§9), nullable
9.  \[“opendma.org”, “Properties”\], multi value, Reference to property info objects (§9), nullable
10. \[“opendma.org”, “Instantiable”\], single value, Boolean, not null
11. \[“opendma.org”, “Hidden”\], single value, Boolean, not null
12. \[“opendma.org”, “System”\], single value, Boolean, not null
13. \[“opendma.org”, ”SubClasses”\], multi value, Reference to a class info objects, nullable

These constraints apply to the properties:

- The restrictions of the “Parent” Property are defined in §8.

- All property info objects (§9) referenced in the “DeclaredProperties” property must be unique in the list of effective properties (§10) of this class regarding the tuple (”NameQualifier”,”Name”). This implies that (a) no qualified property name may be used twice in this list and (b) no qualified property name already used by a parent class or an aspect class may be reused. Properties can not be overwritten.

- The tuple (”NameQualifier”,”Name”) of this object must be unique across all class info objects in the same context (§4)

- The reference to a class info object *c* is contained in the “SubClasses” property of a class info object *p* if and only if the “Parent” reference property of *c* references *p*.

#### §8 Class hierarchy

##### §8.1 Class hierarchy root

Every context (§4) contains at least one class info object (§7) with these property values:

| **Property name**                  | **Value**                                                                                                                                                          |
|:-----------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| \[“opendam.org”, “Class”\]         | Reference to the class class object (§8.2)                                                                                                                         |
| \[“opendam.org”, “Id”\]            | Unique object identifier                                                                                                                                           |
| \[“opendam.org”, “Name”\]          | String “Object”                                                                                                                                                    |
| \[“opendam.org”, “NameQualifier”\] | String “opendma.org”                                                                                                                                               |
| \[“opendma.org”, “Parent”\]        | NULL                                                                                                                                                               |
| \[“opendma.org”, “Aspects”\]       | empty                                                                                                                                                              |
| \[“opendam.org”, “DisplayName”\]   | String “OdmaObject”                                                                                                                                                |
| \[“opendma.org”, “Properties”\]    | Contains at least references to property info objects as described in §9 defining the “Class” property as defined in §6.1 and the “Id” property as defined in §6.2 |

Every context has to provide a reference to exactly one object following these constraints. This object is called the *class hierarchy root*.

##### §8.2 Class class object

Every context (§4) contains a class info object (§7) that is referenced by the class hierarchy root (§8.1) with these property values:

| **Property name**                  | **Value**                                                                                                     |
|:-----------------------------------|:--------------------------------------------------------------------------------------------------------------|
| \[“opendam.org”, “Class”\]         | Reference to itself                                                                                           |
| \[“opendam.org”, “Id”\]            | Unique object identifier                                                                                      |
| \[“opendam.org”, “Name”\]          | String “Class”                                                                                                |
| \[“opendam.org”, “NameQualifier”\] | String “opendma.org”                                                                                          |
| \[“opendma.org”, “Parent”\]        | Reference to the class hierarchy root (§8.1)                                                                  |
| \[“opendma.org”, “Aspects”\]       | empty                                                                                                         |
| \[“opendam.org”, “DisplayName”\]   | String “OdmaClass”                                                                                            |
| \[“opendma.org”, “Properties”\]    | Contains at least references to property info objects as described in §9 defining all properties listed in §7 |

This object is called the *class class*.

<u>Conslusion 1:</u> Every context (§4) contains exactly one class class object. This follows directly from the existence and uniqueness of the class hierarchy root and the single cardinality and not nullability of the Class property of the class hierarchy root.

##### §8.3 Valid class objects

A *valid class object* is a class info object (§7) following these conditions:

1)  The class hierarchy root (§8.1) is a valid class object.
2)  All class objects containing a reference to a valid class object in their “Parent” property are again valid class objects.

This forms a tree like structure called the *OpenDMA class hierarchy*.

##### §8.4 Valid aspect objects

A *valid aspect object* is a class info object (§7) that is not a valid class object (§8.3) and whose “IsInstantiable” property contains the value “false”. This prevents multi inheritance.

<u>Info:</u> Compared to the Java programming language, the valid class objects can be seen as the classes in Java and the valid aspect objects can be seen as the interfaces in Java.

##### §8.5 Extension relationship

A class info object *c* is said to *extend* a class info object *p* if and only if at least one of these conditions is met:

1)  The “Parent” property of *c* references *p*, or
2)  An entry of the Aspects property of *c* references *p*, or
3)  The class info object referenced by *c*’s Parent property extends *p*, or
4)  An entry of the Aspects property of *c* extends *p*.

<u>Info:</u> A class c does not extend itself.

##### §8.6 InstanceOf relationship

A object *o* is said to be an *instance of* a class info object *c* if the “Class” property of *o* contains a reference to a valid class info object that is or extends *c*.

#### §9 Property info object

A *property info object* is an object with at least theses properties:

1.  \[“opendma.org”, “Class”\], single value, Reference, as defined in §6, not null

<!-- -->

14. \[“opendma.org”, “Id”\], single value, String, as defined in §6.2, not null

<!-- -->

2.  \[“opendma.org”, “Name”\], single value, String, not null
3.  \[“opendma.org”, “NameQualifier”\], single value, String, nullable
4.  \[“opendma.org”, “DisplayName”\], single value, String, not null
5.  \[“opendma.org”, “DataType”\], single value, Integer, not null
6.  \[“opendma.org”, “ReferenceClass”\], single value, Reference, nullable
7.  \[“opendma.org”, “MultiValue”\], single value, Boolean, not null
8.  \[“opendma.org”, “Required”\], single value, Boolean, not null
9.  \[“opendma.org”, “Readonly”\], single value, Boolean, not null
10. \[“opendma.org”, “Hidden”\], single value, Boolean, not null
11. \[“opendma.org”, “System”\], single value, Boolean, not null

These constraints apply to the properties:

- There exists exactly one valid class object (§8.3) in each context (§4) that describes property info objects. The qualified name of this object is \[“opendma.org”, “PropertyInfo”\]. The “Class” property has to contain a reference to a valid class object (§8.3) that is or extends this object.

- The value of “DataType” must be one of the list of numeric data type ids (§2.5).

- The value of “ReferenceClass” must contain a refence to a valid class object (§8.3) or a valid aspect object (§8.4) if and only if the value of “DataType” is 8. It must be null otherwise.

#### §10 Effective properties list

The *effective properties list* of a valid class object (§8.3) or valid aspect object (\$8.4) *c* is a list of property info objects (§9) defined as follows:

1)  all property info objects (§9) of *c*’s \[“opendma.org”, “Properties”\] Property are part of the effective properties list, and
2)  all property info objects (§9) of the effective properties list of the class object referenced by *c*’s \[“opendma.org”, “Parent”\] property are part of the effective properties list.
3)  all property info objects (§9) of the effective properties list of the aspect objects referenced by *c*’s \[“opendma.org”, “Aspects”\] property are part of the effective properties list.

#### §11 Failure messages

The object model knows a set of distinguished failure messages for the read / write operations (§5):

1.  ObjectNotFound
2.  InvalidDataType

##### §11.1 Property existence

The read and the write operation (§5) for a qualified property name *pn* on an object *o* have to return an *ObjectNotFound* (§11) response code if and only if the effective property list (§10) of *o* does not contain a property info object that matches in its \[“opendma.org”,”Name”\] and \[“opendma.org”, “NameQualifier”\] values to *pn*.

##### §11.2 Type safety

The write operation (§5) for a qualified property name *pn* on an object *o* has to return an *InvalidDataType* (§11) response code if and only if

1)  the effective property list (§10) of *o* does contain a property info object for *pn*, and
2)  the value of \[“opendam.org”, “DataType”\] of that property info object does not match the data type of the value to be written.

The value returned by the read operation (§5) has to be of the data type defined by the numeric data type id read from the \[“opendam.org”, “DataType”\] property of the corresponding property info for *pn*.

<u>Note1:</u>

This section defines only a required set of properties for the objects of the class hierarchy, but it does not limit the actual properties to this set.

An implementer might introduce additional properties for the class hierarchy root \[“opendma.org”, ”Object”\] without violating the conditions posed by the OpenDMA architecture. This allows the mapping of any existing class hierarchy into the OpenDMA object model.

Later sections of this specification might narrow these constraints and limit the set of properties to exactly the properties defined here.
## Section II: OpenDMA document management model

While the first section has defined a common object oriented model that could represent nearly everything, this second section defines a set of classes and aspects specific for document management.

This set is divided into two parts, a set of basic and a set of extended document management classes and aspects. The first basic set is sufficient for all fundamental document management operations. On top of these, the second set adds extended functionality that is not required in every environment.

### Section II.1: Basic document management model

The set of basic document management classes consists of:

- Repository
  A *Repository* represents a place where all Objects are stored. It represents the *context* defined in §4. Classes inside one Repository need not be valid in another Repository.

- Document
  A *Document* is the atomic element users work on in a content based environment. It can be compared to a file in a file system. But unlike files, it may consist of multiple octet streams. So a webpage can be stored as one Document that consists of the HTML data and all additional image data. Also a TIFF image of a page can be stored as one Document that contains the TIFF image itself as well as a JPEG thumbnail and some XML annotations.
  A Document is able to keep track of its changes (versioning) and manage the access to it (checkin and checkout).

- ContentElement
  A *ContentElement* represents one atomic octet stream the Documents are made of. The binary data is stored together with meta data like the mime type.

- VersionCollection
  A *VersionCollection* represents the set of all versions of a Document.

- Container
  A *Container* holds a list of containable objects that are said to be contained in this Container. This list of containees is build up with Association objects based on references. So an object may be contained in multiple Containers or in no Container at all. OpenDMA does not require a single rooted tree as a file system does.

- Containable
  This aspect is extended by all classes and aspects that can be contained in a Container.

- Folder
  A *Folder* is an extension of the Container that forms one single rooted loop-free tree.

- Association
  An Association represents the directed link between a Container and a Containable object.

#### §12 Repository class

The \[“opendma.org”,”Repository”\] class extends the \[“opendma.org”,”Object”\] class and has these additional properties:

| **Property** | **Type**       | **Contents**                                                                                 |
|:-------------|:---------------|:---------------------------------------------------------------------------------------------|
| Name         | String (s,nn)  | The internal technical name of this repository                                               |
| DisplayName  | String (s, nn) | The name of this Repository displayed to users                                               |
| RootClass    | Reference      | Reference to the class hierarchy root class as defined in §8.1. Must not be null.            |
| RootAspects  | Reference (m)  | Reference to all valid aspect objects that do not inherit another valid aspect               |
| RootFolder   | Reference      | Reference to a Folder aspect if this Repository has a dedicated root folder or null, if not. |

##### §12.1 Repository reflection in the objects

The \[“opendma.org”,”Object”\] class is extended as follows to reflect the Repository they reside in:

| **Property** | **Type**      | **Contents**                          |
|:-------------|:--------------|:--------------------------------------|
| Repository   | Reference (s) | The repository this object belongs to |

#### §13 Global unique identification

The \[“opendma.org”,”Object”\] class is extended as follows:

| **Property** | **Type**       | **Contents**                                                                                                           |
|:-------------|:---------------|:-----------------------------------------------------------------------------------------------------------------------|
| Guid         | String (s, nn) | Global unique identifier for this object. Might be a combination of the Id of the Repository and the Id of the object. |

#### §14 Document aspect

The \[“opendma.org”,”Document”\] aspect is defined as:

| **Property**          | **Type**      | **Contents**                                                                                           |
|:----------------------|:--------------|:-------------------------------------------------------------------------------------------------------|
| VersionSpecificId     | String (s)    | Id identifying this version of the object                                                              |
| VersionSpecificGuid   | Struing (s)   | Guid identifying this version of the object                                                            |
| Title                 | String (s)    | The title of this document                                                                             |
| Version               | String (s)    | Identifier of this version consisting of a set of numbers separated by a point (e.g. 1.2.3)            |
| VersionCollection     | Reference (s) | Reference to the collection of all versions or null if versioning is not supported.                    |
| ContentElements       | Reference (m) | References to multiple ContentElement objects.                                                         |
| CombinedMimeType      | String (s)    | The mime type of the whole Document. It must be calculated from the mime types of each ContentElement. |
| PrimaryContentElement | Reference (s) | Reference to the dedicated primary ContentElement, May only be null if ContentElements is empty.       |
| PrimaryContent        | Content       | Shortcut to the content of PrimaryContentElement                                                       |
| PrimarySize           | Long (s)      | Shortcut to the size of PrimaryContentElement                                                          |
| PrimaryMimeType       | String        | Shortcut to the mimetype of PrimaryContentElement                                                      |
| PrimaryFileName       | String        | Shortcut to the FileName of PrimaryContentElement                                                      |
| CheckedOut            | Boolean (s)   | Whether this document is checked out or not.                                                           |
| CreatedAt             | DateTime (s)  | When this document has been created                                                                    |
| VersionCreatedAt      | DateTime (s)  | When this version of this document has been created                                                    |
| LastModifiedAt        | DateTime (s)  | When *this version* of the document has been created, i.e. when this document has been modified        |
| CheckedOutAt          | DateTime (s)  | When this version of the document has been checked out; null if this document is not checked out       |
| CreatedBy             | String (s)    | User who created this document                                                                         |
| VersionCreatedBy      | String (s)    | User who created this version of this document                                                         |
| LastModifiedBy        | String (s)    | User who created this version of the document                                                          |
| CheckedOutBy          | String (s)    | User who checked out this version of the document; null if this document is not checked out            |

#### §15 ContentElement aspect

The \[“opendma.org”,”ContentElement”\] aspect is defined as:

| **Property** | **Type**    | **Contents**                             |
|:-------------|:------------|:-----------------------------------------|
| Content      | Content (s) | The data of this content element         |
| Size         | Longint (s) | The size of the data in number of octets |
| MimeType     | String (s)  | The mime type of the data                |
| FileName     | String (s)  | The file name of the data                |

#### §16 VersionCollection aspect

The \[“opendma.org”,”VersionCollection”\] aspect is defined as:

| **Property** | **Type**                    | **Contents**                                                                                                        |
|:-------------|:----------------------------|:--------------------------------------------------------------------------------------------------------------------|
| Versions     | Reference (m, nn, Document) | List of all versions of this Document                                                                               |
| Latest       | Reference (s, nn, Document) | Reference to the latest version of the Document collection                                                          |
| Released     | Reference (s, n, Document)  | Reference to the latest released version of this Document Collection. Can be null if no Document has been released. |
| InProgress   | Reference (s, n, Document)  | Reference to the InProgress Document while this document is checked out.                                            |

#### §17 Container aspect

The \[“opendma.org”,”Container”\] aspect is defined as:

| **Property**   | **Type**      | **Contents**                                                      |
|:---------------|:--------------|:------------------------------------------------------------------|
| Title          | String (s)    | The title of this container                                       |
| Containees     | Reference (m) | The containable objects contained in this container               |
| Associations   | Reference (m) | The associations between this container and the contained objects |
| CreatedAt      | DateTime (s)  | When this container has been created                              |
| LastModifiedAt | DateTime (s)  | When this container has been modified the last time               |
| CreatedBy      | String (s)    | User who created this container                                   |
| LastModifiedBy | String (s)    | User who modified this container the last time                    |

#### §18 Folder aspect

The \[“opendma.org”,”Folder”\] aspect extends the \[“opendma.org”,”Container”\] by these additional properties:

| **Property** | **Type**               | **Contents**                                  |
|:-------------|:-----------------------|:----------------------------------------------|
| Parent       | Reference (s,n,Folder) | The parent folder this folder is contained in |

The Parent property of all Folder objects in the repository, except for the Folder referenced in the RootFolder property of the Repository (§13), must not be null. The parent property of the Folder referenced in the RootFolder property of the Repository (§13) must be null.

A folder *d* is called a *descendant* of folder *f* if one of these conditions is met:

1)  The “Parent” property of *d* references *f*, or
2)  The “Parent” property of *d* references a descendant of *f*.

A Folder must not contain a reference to one of its descendants in its Parent property.

#### §19 Containable aspect

The \[“opendma.org”,”Containable”\] aspect is defined as:

| **Property**            | **Type**      | **Contents**                                                         |
|:------------------------|:--------------|:---------------------------------------------------------------------|
| ContainedIn             | Reference (m) | The container objects this Containable is contained in               |
| ContainedInAssociations | Reference (m) | The associations that bind this Containable in the Conatiner objects |

#### §20 Association aspect

The \[“opendma.org”,”Association”\] aspect is defined as:

| **Property**   | **Type**                      | **Contents**                                                                                    |
|:---------------|:------------------------------|:------------------------------------------------------------------------------------------------|
| Name           | String (s)                    | The name of this association                                                                    |
| Container      | Reference (s,nn, Container)   | The source of this directed association                                                         |
| Containment    | Reference (s,nn, Containable) | The destination of this directed association                                                    |
| CreatedAt      | DateTime (s)                  | When this document has been created                                                             |
| LastModifiedAt | DateTime (s)                  | When *this version* of the document has been created, i.e. when this document has been modified |
| CreatedBy      | String (s)                    | User who created this document                                                                  |
| LastModifiedBy | String (s)                    | User who created this version of the document                                                   |

### Section II.2: Extended document management model

The set of extended document management classes consists of:

- AccessControl
  Describe me.

- Rendition
  Describe me.

- Policy
  Describe me.

- Retention
  Describe me.