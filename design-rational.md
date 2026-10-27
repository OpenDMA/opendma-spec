
# Design Rational for OpenDMA

## Preface

The Open document management architecture (OpenDMA) is based on an *abstract* architecture that is able to cover all existing document management systems. This abstract architecture is defined in multiple layers (*sections* in this document), where each layer is built on top of features offered by lower layers.

## Section I: Object oriented OpenDMA model

OpenDMA uses a strongly typed reflective object oriented data model with a class hierarchy. Section I.1 first defines a *simple object model* that has as few constraints as possible. Section I.2 then adds the constraints and reflection needed by OpenDMA.

### Section I.1: Simple object model

This first subsection defines an object model without any constraints. Those objects are often referred to as *soft objects*.

#### §1 Qualified names

We need to uniquely identify modelling entities, like Classes, Types, Properties, Constraints, and the like.
In contrast to the entities under management, there are only few of them.

String based identifier:  
Most vendors use human readable string based identifiers. A few use numeric identifiers, which van easily represented as text strings.

Qualified names:  
There are common terms used by multiple vendors (e.g. "Document", "Folder", "Type", "Item", "Node", "Class", ...).
We want to be able to federate content from multiple repositories, which causes naming conflicts. Even without federation,
there are potential naming conflicts between this standard and identifiers used by vendors. The introduction of qualified
names resolves these conflicts.

String based namespaces:  
Alfresco follows the XML namespace design and uses URIs as qualifiers. While this guarantees unique namespaces without a central
registration authority, it turned out to be cumbersome. Most users use the `cm:content` short form instead of the correct full
qualified name `{http://www.alfresco.org/model/content/1.0}content`. In the end, this made the situation even worse.  
There are simply ways to guarantee unique namespaces even with short sings, e.g. by using a reverse domain format like `com.example`. 

Nested namespaces:  
Some vendors already use namespaces, either explicit (Alfresco) or implicit (P8). Nested namespaces allow us to map identifiers
of these systems into a common naming schema (e.g. `alfresco:cm:content`). Nested namespaces allows implementors to naturally
organise their identifiers.  

#### §2 Data types

Data types are choosen to cover all data types found in major ECM systems (see [data type research](./research/data-types.md)).

##### §2.1 Scalar values

The following data types are used by vendors and are currently not natively supported:

- Decimal (cannot really by covered by floating point numbers, **should be added in next version**)
- Currency (could be represented by decimal)
- Date only (can be represented by DataTime with time component set to zero; enforcing DateTime in UTC helps to avoid timezone conversion issues)
- Time only (can be represented as a String)
- ID (can either be represented as String or as Reference)
- CLOB (character large object; can be represented as Binary in a fixed character encoding, like UTF-8)
- User or group reference (can be represented as String field containing the UUID)
- multilingual text (can be realised as multi-valued reference to objects combining locale and localised string)

##### §2.2 Reference values

Many vendors, especially those  with an object oriented data model, support some mechanism how an entity can reference another entity.

References between objects allows us to model complex metadata structures (attribute groups in ICM, sets in Livelink, complex types in Nuxeo,
keyword groups in OnBase) as linked structure of objects.

See [entity relationships research](./research/entity-relationships.md).

##### §2.3 Content

Modelling and storage of large binary data streams is very diverse (see [content modelling research](./research/data-types.md)).

Having “Content” as first class data type allows adaptor implementors to map any content model into OpenDMA while application
developers build vendor agnostic applications.

##### §2.4 Cardinality

Almost all vendors support single and multi valued data types. If supported, scalar values are almost always realised as ordered list.

Vendors that support multi  valued references to other entities often represent them as unordered set, hence we cannot guarantee an ordered list constraint here.

See [cardinality research](./research/cardinality_nullability.md).

##### §2.5 Data type id

For property info objects, we need a way to represent the data type in a format that can be exposed as property. Typical
candidates are strings and integers.

We decided for a numeric representation to avoid potential ambiguity around case.

##### §2.6 Nullability

The exact definition of a representation for “not assigned” / "empty" has been choosen so that adaptor implementors can map
the behaviour of existing systems to OpenDMA.

See [nullability research](./research/cardinality_nullability.md).

#### §3 Properties

The definition of "Properties" follows the definition of attributes / fields / keywords / properties as used by various vendors.

See [entity model research](./research/entity-model.md).

#### §4 Objects

The definition of "Objects" follows object oriented design principles. It has been designed so that Adaptor implementors
can represent the items / objects / nodes / records found in various systems as objects.

Complex metadata (sets in Livelink, attribute groups in ICM, ...) can be designed as linked structure of objects. For example,
a child component in ICM can be designed as its own object. The root component of the item type would then hold a reference
property pointing to the  set of objects representing the child component.

The unique object identifier has been choosen as String to allow identifiers of all vendors to be represented in OpenDMA (integer, GUID, ...).

See [entity model research](./research/entity-model.md).

#### §5 Operations on objects

We explicitly defined the Read and Write operations to avoid ambiguity.

> [!NOTE]  
> Creation and removal of entire objects is not yet standardised.

### Section I.2: OpenDMA object model

We want to enable application developers to inspect the data model through OpenDMA. To keep the model simple, we want to use
the same basic objects to expose information about  the data model.

This follows the design in many programming languages. In Java for example, there is the class `java.lang.Class` exposing
information about the fields and methods of objects of this class. Every object in Java has a `getClass()` method returning
an object of  `java.lang.Class` describing the object. C#, Python, and many other languages use the same principle.

P8 (ClassDescription) and Documentum (dm_type) applied this principle to ECM as well.

See [data modelling research](./research/data-modelling.md).

#### §6 Basic object constraints

##### §6.1 Reflection

In contrast to objects and classes in programming languages, many ECM vendors support additional sources
of layout and behaviour definitions.

Categories in Livelink, Aspects in Alfresco, or Facets in Nuxeo allow to tweak the layout and behaviour
of individual objects in addition to their type.

With classes and aspects, we establish two sources of layout definitions that are able to cover existing ECM systems.

We allow the reference property to be constraint to objects of a particular class or having a particular aspect.
This allows application developers to implement specific handling of these properties.

See [data modelling research](./research/data-modelling.md).

##### §6.2 Identification

All unique entity identifiers used by different vendors can be mapped to and from String values.

See [entity model research](./research/entity-model.md).

#### §7 Class info object

To support flexible composition without relying on rigid class inheritance hierarchies, this standard
introduces "Aspects". An aspect can define a set of properties which can be added to a class as a whole
be "including" the aspect in the class. This polymorphism fosters the reuse of property sets.

More importantly, it allows this standard to define common ECM constructs like "Documents" and "Folders"
without enforcing a specific class hierarchy. This allows adaptors to map the original class hiararchy
of systems like P8 or Documentum directly to OpenDMA.

OpenDMA uses the same aspects for ploymorphism in the class hierarchy as well as for the extension
of individual objects (see §6.1).

This level of the spec uses duck typing: every object that has a defined set of properties is considered
a "Class".

#### §8 Class hierarchy

To be able to cover ECM systems like Documentum, P8, Alfresco, Nuxeo, FileNet and others, OpenDMA
nneds to support a class hierarchy, nut just a flat list of types.

See [data modelling research](./research/data-modelling.md).

##### §8.1 Class hierarchy root

OpenDMA defines the `opendma:Object` class as root of the class hierarchy. It defines the properties described in §6.1 and §6.2.

##### §8.2 Class class object

For reflection, OpenDMA defines the `opendma:Class` class directly inheriting from the class hiararchy root.
It adds all properties defined in §7.

##### §8.3 Valid class objects

OpenDMA uses duck typing to define class objects.

This section defines a subset of *valid class objects* as objects that are part of the *OpenDMA class hierarchy*.

##### §8.4 Valid aspect objects

OpenDMA uses duck typing to define class objects.

This section defines a subset of *valid aspect objects*.

##### §8.5 Extension relationship

Exact definition to avoid amiguity in implementations. Following object oriented design principles.

##### §8.6 InstanceOf relationship

Exact definition to avoid amiguity in implementations. Following object oriented design principles.

#### §9 Property info object

Object describing properties to be used in the reflection mechanism. Standard uses duck typing.

#### §10 Effective properties list

Exact definition to avoid amiguity in implementations. Following object oriented design principles.

#### §11 Choice value object

Many ECM systems support constraints on properties that limit the allowed values to a small
predefined set (Menu in ImageServices, Choice lists in P8, popups in Livelink, Choice in Sharepoint, ...).

This object describes valid values. We allow these choices for all data types.

#### §12 Failure messages

To enforce consistency between implementations and help application developers, we define error
situations and feedback.

#### §13 Core class reference

Section I.2 defines a set of properties that are required for the objects of the class hierarchy, but it does not limit the actual properties to this set.

An implementer might introduce additional properties for the class hierarchy root `opendma:Object` without violating the conditions posed by OpenDMA.
This allows the mapping of any existing class hierarchy into the OpenDMA object model.

The meaning and expected content of the properties defined in section I.2 is documented in this paragraph.

## Section II: OpenDMA document management model

While the first section has defined a common object oriented model that could represent nearly everything, this second section defines a set of classes and aspects specific for document management.

This set is divided into two parts, a set of basic and a set of extended document management classes and aspects. The first basic set is sufficient for all fundamental document management operations. On top of these, the second set adds extended functionality that is not required in every environment.

### Section II.1: Basic document management model

OpenDMA defines a minimal set of entities that are required to map existing ECM systems to an abstract architecture.

See [entity model research](./research/entity-model.md).

Repository:  
Some ECM systems introduce some sort of data isolation boundary (e.g. object stores in P8, sites in Sharepoint). In addition,
OpenDMA needs a single starting point which exposes references to the class hierarchy, aspects, filesystem, and other entities.
The repository is defined as a class.

The following entities are all designed as an aspects to allow the whole original class hiararchy to be mapped as decendant
of `opendma:Object`.

Document:  
The primary logical entity for items under management. Handles content and versioning.

Content:  
See [content representation research](./research/content.md).

Versioning:  
See [versioning research](./research/versioning.md).

Filesystem:  
The standard defines a basic  Container and then specialises it into Folder to be able to cover different
conatinment strategies.  
See [filesystem research](./research/versioning.md).

AuditStamped aspect:  
Documents, Folders and other entities typically expose information about their creation and last update. These are
modelled as separate aspect rather than included in the `opendma:Document` and `opendma:Folder` aspects to
foster property reuse and avoid conflicts in systems that allow items to be a Folder and a Document simultaniously.

### Section II.2: Extended document management model

The design of extended document management classes and aspects has been choosen to cover commonalities of these
features between different vendors.

## Section III: OpenDMA search model

OpenDMA separates full-text search from metadata based queries.

### Section III.1: OpenDMA Full-Text Search Expression

The Full-Text Search Expression intentionally defines a small common subset of full-text search capabilities that
can be mapped to a broad range of Enterprise Content Management and Document Management systems.

The syntax separates portable OpenDMA semantics from native query-language behavior.

In particular, OpenDMA defines adjacent positive expressions as conjunction even though underlying systems may use
conjunction, disjunction, mandatory-term markers, query modes, or explicit Boolean operators to express the same condition.

Likewise, OpenDMA defines its own operator precedence and grouping semantics independently of the precedence rules
of native query languages.

This allows applications to construct one Full-Text Search Expression and use it consistently through different OpenDMA
adaptors without depending on the syntax or defaults of the underlying document management system.

### Section III.2: OpenDMA Object Search Expression

The OpenDMA Object Search Expression complements FTSE and defines a common subset of metadata based queries.

It uses JSON objects to avoid the definition of another SQL-like grammer.

Primitives and boolean conjunctions for filter expressions are straight forward.

OpenDMA intentionally does not introduce primitives for full-text, folder hierarchy or class constraints.
Instead, these are separated into distinct conditions for class, scope, metadata filter, and fulltext.

## Copyright and Licensing

Copyright :copyright: 2008-present, xaldon Technologies GmbH

The OpenDMA Specification is licensed under the [Open Web Foundation Agreement 1.0 (Patent and Copyright Grants)](https://www.openwebfoundation.org/the-agreements/the-owf-1-0-agreements-granted-claims/owfa-1-0)
