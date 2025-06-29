|                           |                          |
|---------------------------|-------------------------:|
| xaldon Technologies GmbH. |   TECH-DOC: **ODMA-002** |
| Editor: Stefan Kopf       |        Version 0.7 DRAFT |
|                           |               **DARAFT** |

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
2.  a name (noncolon Unicode text string with 1 or more characters; must not be null),

where the qualifier can be omitted (null).

The qualifier is a sequence of noncolon names connected by the colon character. Each nc name identifies one namespace. The namespaces are nested from left to right.

The namespace “opendma” is reserved for this architecture specification.

If a qualified name is represented as simple string, the qualifier and the name are concatenated by the colon character.

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

Every object must have at least a single valued property (§3) with the qualified name `opendma:Class`. This property is a reference property (§2.2) that has to contain a reference to a *valid class object* (§8.3). The property “IsInstantiable” of that valid class object must be true.

The properties of every object must exactly match in number, data type, cardinality and nullability the *effective property list* (§10) defined by this valid class object. A reference property *x* (§2.2) must only contain references to objects whose “Class” Property contains a reference to a class info object that is or extends (§8.4) the class info object referenced by the “ReferenceClass” property of *x*’s property info object (§9).

##### §6.2 Identification

Every object must have at least a single valued String property (§3) with the qualified name `opendma:Id`. This property contains the String representation of the unique object identifier as defined in §4.

#### §7 Class info object

A *class info object* is an object with at least theses properties:

1.  `opendma:Class`, single value, Reference, as defined in §6.1, not null
2.  `opendma:Id`, single value, String, as defined in §6.2, not null
3.  `opendma:Name`, single value, String, not null
4.  `opendma:NameQualifier`, single value, String, nullable
5.  `opendma:DisplayName`, single value, String, not null
6.  `opendma:SuperClass`, single value, Reference to a class info object (§7), nullable
7.  `opendma:Aspects`, multi value, Reference to valid aspect objects (§8.4), nullable
8.  `opendma:DeclaredProperties`, multi value, Reference to property info objects (§9), nullable
9.  `opendma:Properties`, multi value, Reference to property info objects (§9), nullable
10. `opendma:Aspect`, single value, Boolean, not null
11. `opendma:Instantiable`, single value, Boolean, not null
12. `opendma:Hidden`, single value, Boolean, not null
13. `opendma:System`, single value, Boolean, not null
14. `opendma:Retrievable`, single value, Boolean, not null
15. `opendma:Searchable`, single value, Boolean, not null
16. `opendma:SubClasses`, multi value, Reference to a class info objects, nullable

These constraints apply to the properties:

- The restrictions of the “SuperClass” Property are defined in §8.

- All property info objects (§9) referenced in the “DeclaredProperties” property must be unique in the list of effective properties (§10) of this class regarding the tuple (”NameQualifier”,”Name”). This implies that (a) no qualified property name may be used twice in this list and (b) no qualified property name already used by a super class or an aspect class may be reused. Properties can not be overwritten.

- The tuple (”NameQualifier”,”Name”) of this object must be unique across all class info objects in the same context (§4)

- The reference to a class info object *c* is contained in the “SubClasses” property of a class info object *p* if and only if the “SuperClass” reference property of *c* references *p*.

#### §8 Class hierarchy

##### §8.1 Class hierarchy root

Every context (§4) contains at least one class info object (§7) with these property values:

| **Property name**     | **Value**                                                                                                                                                          |
|:----------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `opendma:Class`         | Reference to the class class object (§8.2)                                                                                                                         |
| `opendma:Id`            | Unique object identifier                                                                                                                                           |
| `opendma:Name`          | String “Object”                                                                                                                                                    |
| `opendma:NameQualifier` | String “opendma”                                                                                                                                                   |
| `opendma:SuperClass`    | NULL                                                                                                                                                               |
| `opendma:Aspects`       | empty                                                                                                                                                              |
| `opendma:DisplayName`   | String “OdmaObject”                                                                                                                                                |
| `opendma:Properties`    | Contains at least references to property info objects as described in §9 defining the “Class” property as defined in §6.1 and the “Id” property as defined in §6.2 |

Every context has to provide a reference to exactly one object following these constraints. This object is called the *class hierarchy root*.

##### §8.2 Class class object

Every context (§4) contains a class info object (§7) that is referenced by the class hierarchy root (§8.1) with these property values:

| **Property name**     | **Value**                                                                                                     |
|:----------------------|:--------------------------------------------------------------------------------------------------------------|
| `opendma:Class`         | Reference to itself                                                                                           |
| `opendma:Id`            | Unique object identifier                                                                                      |
| `opendma:Name`          | String “Class”                                                                                                |
| `opendma:NameQualifier` | String “opendma”                                                                                              |
| `opendma:SuperClass`    | Reference to the class hierarchy root (§8.1)                                                                  |
| `opendma:Aspects`       | empty                                                                                                         |
| `opendma:DisplayName`   | String “OdmaClass”                                                                                            |
| `opendma:Properties`    | Contains at least references to property info objects as described in §9 defining all properties listed in §7 |

This object is called the *class class*.

<u>Conslusion 1:</u> Every context (§4) contains exactly one class class object. This follows directly from the existence and uniqueness of the class hierarchy root and the single cardinality and not nullability of the Class property of the class hierarchy root.

##### §8.3 Valid class objects

A *valid class object* is a class info object (§7) following these conditions:

1)  The class hierarchy root (§8.1) is a valid class object.
2)  All class objects containing a reference to a valid class object in their “SuperClass” property are again valid class objects.

This forms a tree like structure called the *OpenDMA class hierarchy*. The “Aspect” property of every valid clas object has to contain the value “false”.

##### §8.4 Valid aspect objects

A *valid aspect object* is a class info object (§7) that is not a valid class object (§8.3), whose “Aspect” property contains the value “true” and whose “Instantiable” property contains the value “false”. This prevents multi inheritance.

<u>Info:</u> Compared to the Java programming language, the valid class objects can be seen as the classes in Java and the valid aspect objects can be seen as the interfaces in Java.

##### §8.5 Extension relationship

A class info object *c* is said to *extend* a class info object *p* if and only if at least one of these conditions is met:

1)  The “SuperClass” property of *c* references *p*, or
2)  An entry of the Aspects property of *c* references *p*, or
3)  The class info object referenced by *c*’s SuperClass property extends *p*, or
4)  An entry of the Aspects property of *c* extends *p*.

<u>Info:</u> A class c does not extend itself.

##### §8.6 InstanceOf relationship

A object *o* is said to be an *instance of* a class info object *c* if the “Class” property of *o* contains a reference to a valid class info object that is or extends *c*.

#### §9 Property info object

A *property info object* is an object with at least theses properties:

1.  `opendma:Class`, single value, Reference, as defined in §6, not null

<!-- -->

17. `opendma:Id`, single value, String, as defined in §6.2, not null

<!-- -->

2.  `opendma:Name`, single value, String, not null
3.  `opendma:NameQualifier`, single value, String, nullable
4.  `opendma:DisplayName`, single value, String, not null
5.  `opendma:DataType`, single value, Integer, not null
6.  `opendma:ReferenceClass`, single value, Reference, nullable
7.  `opendma:MultiValue`, single value, Boolean, not null
8.  `opendma:Required`, single value, Boolean, not null
9.  `opendma:ReadOnly`, single value, Boolean, not null
10. `opendma:Hidden`, single value, Boolean, not null
11. `opendma:System`, single value, Boolean, not null
12. `opendma:Choices`, multi value, Reference, nullable

These constraints apply to the properties:

- There exists exactly one valid class object (§8.3) in each context (§4) that describes property info objects. The qualified name of this object is `opendma:PropertyInfo`. The “Class” property has to contain a reference to a valid class object (§8.3) that is or extends this object.

- The value of “DataType” must be one of the list of numeric data type ids (§2.5).

- The value of “ReferenceClass” must contain a refence to a valid class object (§8.3) or a valid aspect object (§8.4) if and only if the value of “DataType” is 8. It must be null otherwise.

#### §10 Effective properties list

The *effective properties list* of a valid class object (§8.3) or valid aspect object (\$8.4) *c* is a list of property info objects (§9) defined as follows:

1)  all property info objects (§9) of *c*’s `opendma:Properties` Property are part of the effective properties list, and
2)  all property info objects (§9) of the effective properties list of the class object referenced by *c*’s `opendma:SuperClass` property are part of the effective properties list.
3)  all property info objects (§9) of the effective properties list of the aspect objects referenced by *c*’s `opendma:Aspects` property are part of the effective properties list.

#### §11 Choice value object

A *choice value object* is an object with at least theses properties:

13. `opendma:Class`, single value, Reference, as defined in §6, not null

<!-- -->

18. `opendma:Id`, single value, String, as defined in §6.2, not null

<!-- -->

14. `opendma:DisplayName`, single value, String, not null
15. `opendma:StringValue`, single value, String, nullable
16. `opendma:IntegerValue`, single value, Integer, nullable
17. `opendma:ShortValue`, single value, Short, nullable
18. `opendma:LongValue`, single value, Long, nullable
19. `opendma:FloatValue`, single value, Float, nullable
20. `opendma:DoubleValue`, single value, Double, nullable
21. `opendma:BooleanValue`, single value, Boolean, nullable
22. `opendma:DateTimeValue`, single value, DateTime, nullable
23. `opendma:BlobValue`, single value, BLOB, nullable
24. `opendma:ReferenceValue`, single value, Reference, nullable

#### §12 Failure messages

The object model knows a set of distinguished failure messages for the read / write operations (§5):

1.  ObjectNotFound
2.  InvalidDataType

##### §12.1 Property existence

The read and the write operation (§5) for a qualified property name *pn* on an object *o* have to return an *ObjectNotFound* (§11) response code if and only if the effective property list (§10) of *o* does not contain a property info object that matches in its `opendma:Name` and `opendma:NameQualifier` values to *pn*.

##### §12.2 Type safety

The write operation (§5) for a qualified property name *pn* on an object *o* has to return an *InvalidDataType* (§11) response code if and only if

1)  the effective property list (§10) of *o* does contain a property info object for *pn*, and

2)  one of these conditions applies:

    1.  the value of `opendma:DataType` of that property info object does not match the data type of the value to be written, or
    2.  the value of `opendma:Choices` of that property info object is not null and does not contain a reference to a choice value object (§11) whose value property corresponding to the data type contains the value to be written.

The value returned by the read operation (§5) has to be of the data type defined by the numeric data type id read from the `opendma:DataType` property of the corresponding property info for *pn*.

<u>Note1:</u>

This section defines only a required set of properties for the objects of the class hierarchy, but it does not limit the actual properties to this set.

An implementer might introduce additional properties for the class hierarchy root `opendma:Object` without violating the conditions posed by the OpenDMA architecture. This allows the mapping of any existing class hierarchy into the OpenDMA object model.

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
  A *ContentElement* represents one atomic content element the Documents are made of. This abstract (non instantiable) base class defines the type of content and the position of this element in the sequence of all content elements.

- DataContentElement
  A *DataContentElement* represents one atomic octet stream. The binary data is stored together with meta data like size and filename.

- ReferenceContentElement
  A *ReferenceContentElement* represents a reference to external data. The reference is stored as URI to the content location.

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

#### §13 Repository class

The `opendma:Repository` class extends the `opendma:Object` class and has these additional properties:

| **Property** | **Type**  | **C** | **N** | **Contents**                                                                                                                 |
|:-------------|:----------|:------|:------|:-----------------------------------------------------------------------------------------------------------------------------|
| Name         | String    | S     | N     | The internal technical name of this repository                                                                               |
| DisplayName  | String    | S     | N     | The name of this Repository displayed to users                                                                               |
| RootClass    | Reference | S     | N     | Reference to the class hierarchy root class as defined in §8.1. Must not be null. Reference class: `opendma:Class`             |
| RootAspects  | Reference | M     | Y     | Reference to all valid aspect objects that do not inherit another valid aspect Reference class: `opendma:Class`                |
| RootFolder   | Reference | S     | Y     | Reference to a Folder aspect if this Repository has a dedicated root folder or null, if not. Reference class: `opendma:Folder` |

##### §13.1 Repository reflection in the objects

The `opendma:Object` class is extended as follows to reflect the Repository they reside in:

| **Property** | **Type**  | **C** | **N** | **Contents**                                                              |
|:-------------|:----------|:------|:------|:--------------------------------------------------------------------------|
| Repository   | Reference | S     | N     | The repository this object belongs to Reference class: `opendma:Repository` |

#### §14 Global unique identification

The `opendma:Object` class is extended as follows:

| **Property** | **Type** | **C** | **N** | **Contents**                                                                                                           |
|:-------------|:---------|:------|:------|:-----------------------------------------------------------------------------------------------------------------------|
| Guid         | String   | S     | N     | Global unique identifier for this object. Might be a combination of the Id of the Repository and the Id of the object. |

#### §15 Document aspect

The `opendma:Document` aspect is defined as:

| **Property**           | **Type**  | **C** | **N** | **Contents**                                                                                                                                    |
|:-----------------------|:----------|:------|:------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| Title                  | String    | S     | Y     | The title of this document                                                                                                                      |
| Version                | String    | S     | Y     | Identifier of this version consisting of a set of numbers separated by a point (e.g. 1.2.3)                                                     |
| VersionCollection      | Reference | S     | Y     | Reference to the collection of all versions or null if versioning is not supported. Reference class: `opendma:VersionCollection`                  |
| VersionIndependentId   | String    | S     | N     | Id identifying this logical document independent from the specific version                                                                      |
| VersionIndependentGuid | String    | S     | N     | Guid identifying this logical document independent from the specific version                                                                    |
| ContentElements        | Reference | M     | Y     | References to multiple ContentElement objects. Reference class: `opendma:ContentElement`                                                          |
| CombinedContentType    | String    | S     | Y     | The mime type of the whole Document. It must be calculated from the mime types of each ContentElement.                                          |
| PrimaryContentElement  | Reference | S     | Y     | Reference to the dedicated primary ContentElement, May only be null if ContentElements is empty (null). Reference class: `opendma:ContentElement` |
| CreatedAt              | DateTime  | S     | N     | When this version of this document has been created                                                                                             |
| CreatedBy              | String    | S     | N     | User who created this version of this document                                                                                                  |
| LastModifiedAt         | DateTime  | S     | N     | When this version of this document has been modified the last time                                                                              |
| LastModifiedBy         | String    | S     | N     | User who modified this version of this document the last time                                                                                   |
| CheckedOut             | Boolean   | S     | N     | Whether this document is checked out or not.                                                                                                    |
| CheckedOutAt           | DateTime  | S     | Y     | When this version of the document has been checked out; null if and only if this document is not checked out                                    |
| CheckedOutBy           | String    | S     | Y     | User who checked out this version of this document; null if and only if this document is not checked out                                        |

#### §16 ContentElement aspect

The `opendma:ContentElement` aspect is defined as:

| **Property** | **Type** | **C** | **N** | **Contents**                                                                                |
|:-------------|:---------|:------|:------|:--------------------------------------------------------------------------------------------|
| ContentType  | String   | S     | Y     | The MIME type of the content represented by this element                                    |
| Position     | Integer  | S     | Y     | The position of this element in the list of all content elements of the containing document |

#### §17 DataContentElement aspect

The `opendma:DataContentElement` aspect extends opendam:ContentElement and declares:

| **Property** | **Type** | **C** | **N** | **Contents**                             |
|:-------------|:---------|:------|:------|:-----------------------------------------|
| Content      | Content  | S     | Y     | The data of this content element         |
| Size         | Longint  | S     | Y     | The size of the data in number of octets |
| FileName     | String   | S     | Y     | The file name of the data                |

#### §18 ReferenceContentElement aspect

The `opendma:ReefernceContentElement` aspect extends opendam:ContentElement and declares:

| **Property** | **Type** | **C** | **N** | **Contents**                        |
|:-------------|:---------|:------|:------|:------------------------------------|
| Location     | String   | S     | Y     | The URI where the content is stored |

#### §16 VersionCollection aspect

The `opendma:VersionCollection` aspect is defined as:

| **Property** | **Type**  | **C** | **N** | **Contents**                                                                                                                                          |
|:-------------|:----------|:------|:------|:------------------------------------------------------------------------------------------------------------------------------------------------------|
| Versions     | Reference | M     | N     | List of all versions of this Document Reference class: `opendma:Document`                                                                               |
| Latest       | Reference | S     | Y     | Reference to the latest version of the Document collection Reference class: `opendma:Document`                                                          |
| Released     | Reference | S     | Y     | Reference to the latest released version of this Document Collection. Can be null if no Document has been released. Reference class: `opendma:Document` |
| InProgress   | Reference | S     | Y     | Reference to the InProgress Document while this document is checked out. Reference class: `opendma:Document`                                            |
| CreatedAt    | DateTime  | S     | N     | When this document has been created                                                                                                                   |
| CreatedBy    | String    | S     | N     | User who created this document                                                                                                                        |

#### §17 Container aspect

The `opendma:Container` aspect is defined as:

| **Property**   | **Type**  | **C** | **N** | **Contents**                                                                                           |
|:---------------|:----------|:------|:------|:-------------------------------------------------------------------------------------------------------|
| Title          | String    | S     | Y     | The title of this container                                                                            |
| Containees     | Reference | M     | Y     | The containable objects contained in this container Reference class: `opendma:Containable`               |
| Associations   | Reference | M     | Y     | The associations between this container and the contained objects Reference class: `opendma:Association` |
| CreatedAt      | DateTime  | S     | N     | When this container has been created                                                                   |
| CreatedBy      | String    | S     | N     | User who created this container                                                                        |
| LastModifiedAt | DateTime  | S     | N     | When this container has been modified the last time                                                    |
| LastModifiedBy | String    | S     | N     | User who modified this container the last time                                                         |

#### §18 Folder aspect

The `opendma:Folder` aspect extends the `opendma:Container` by these additional properties:

| **Property** | **Type**  | **C** | **N** | **Contents**                                                                                                 |
|:-------------|:----------|:------|:------|:-------------------------------------------------------------------------------------------------------------|
| Parent       | Reference | S     | Y     | The parent folder this folder is contained in Reference class: `opendma:Folder`                                |
| SubFolders   | Reference | M     | Y     | All Folder objects that contain this folder in their `opendma:Parent` property Reference class: `opendma:Folder` |

The Parent property of all Folder objects in the repository, except for the Folder referenced in the RootFolder property of the Repository (§13), must not be null. The parent property of the Folder referenced in the RootFolder property of the Repository (§13) must be null.

A folder *d* is called a *descendant* of folder *f* if one of these conditions is met:

1)  The “Parent” property of *d* references *f*, or
2)  The “Parent” property of *d* references a descendant of *f*.

A Folder must not contain a reference to one of its descendants in its Parent property.

#### §19 Containable aspect

The `opendma:Containable` aspect is defined as:

| **Property**            | **Type**  | **C** | **N** | **Contents**                                                                                              |
|:------------------------|:----------|:------|:------|:----------------------------------------------------------------------------------------------------------|
| ContainedIn             | Reference | M     | Y     | The container objects this Containable is contained in Reference class: `opendma:Container`                 |
| ContainedInAssociations | Reference | M     | Y     | The associations that bind this Containable in the Conatiner objects Reference class: `opendma:Association` |

#### §20 Association aspect

The `opendma:Association` aspect is defined as:

| **Property**   | **Type**  | **C** | **N** | **Contents**                                                                                    |
|:---------------|:----------|:------|:------|:------------------------------------------------------------------------------------------------|
| Name           | String    | S     | N     | The name of this association                                                                    |
| Container      | Reference | S     | N     | The source of this directed association Reference class: `opendma:Container`                      |
| Containable    | Reference | S     | N     | The destination of this directed association Reference class: `opendma:Containable`               |
| CreatedAt      | DateTime  | S     | N     | When this document has been created                                                             |
| CreatedBy      | String    | S     | N     | User who created this document                                                                  |
| LastModifiedAt | DateTime  | S     | N     | When *this version* of the document has been created, i.e. when this document has been modified |
| LastModifiedBy | String    | S     | N     | User who created this version of the document                                                   |

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

Template property table

| **Property** | **Type** | **C** | **N** | **Contents** |
|:-------------|:---------|:------|:------|:-------------|
|              |          |       |       |              |
|              |          |       |       |              |
|              |          |       |       |              |
|              |          |       |       |              |
|              |          |       |       |              |
|              |          |       |       |              |
|              |          |       |       |              |
|              |          |       |       |              |
|              |          |       |       |              |
|              |          |       |       |              |
|              |          |       |       |              |
|              |          |       |       |              |
|              |          |       |       |              |
|              |          |       |       |              |
|              |          |       |       |              |
|              |          |       |       |              |
|              |          |       |       |              |
|              |          |       |       |              |
|              |          |       |       |              |
|              |          |       |       |              |