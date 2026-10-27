# Entity model

## FileNet Image Services

Each entity under management is referred to as a **Document**.

Every document belongs to exactly one document class (**DocClass**).

Each document class defines a set of index fields (properties), each with a name and data type.

Index fields are defined globally and are then associated with document classes.

## FileNet P8

All entities are repsesented as an **object**. An object is made up of a list of **properties**.
Each property has a **symbolic name**, a data type, and can be of single or multi cardinality.

One object can reference another object with the `OBJECT` data type.

Each object is of exactly one **Class**. Every object must have a property named "CalssDescription"
of type `OBJECT` which points to an object describing this class.

Object can be independent or dependent. Independent objects must have a unique `Id` property.
Dependent objects can only be accessed through an independent obejcts which references the
dependent objects. It is also possible to describe the independent object to "contain" the
dependent object as an embedded object. Dependent objects can only be created, updated and
deleted as part of the independent object which  references / contains them.

Objects can be ephemeral or persisted. Only persisted objects are stored in the database. Ephemeral
objects are created on-the-fly e.g. to represent users retrieved from LDAP.

## IBM Content Manager (ICM) aka DB2 Content Manager

Each entity under management is reffered to as an **item**.
Every item is of exactly one **item type**. The item type defines the attributes that make up the item.

Attributes are defined globally with their name, display name and data type.
Attributes are then associated with item types. When an attribute is associated with an item type,
it can be defined if the attribute is required and if the attribute needs to have a unique value across
all items of this item type.

As part of the attributes, an item type can have **child components**. Each component is defined with a
component name, minimum and maximum cardinality. Each component can be associated with attributes and
nested child components.

Technically, an item type does not define any attributes. Instead, it uses exactly one root component.
A component then defines an ordered list of attributes and child components.

Binary information is stored in items in **item parts**. Each item can have zero or multiple item parts.
each item part is of exactly one **part type**. The item type defines which part types are available for
items of this type.

There is a fixed set of system defined part types: ICMBASE, ICMBASETEXT, ICMBASESTREAM, ICMNOTELOG, ICMANNOTATION.

## Content Manager OnDemand (CMOD)

Entities under management are referred to as a **document**.

Documents can come from different sources, each defined in the system as an **application**. Documents are
ingested automatically from the print output of these applications. The definition of an application in
CMOD mainly defines rules how individual documents are separated in the print stream and how index fields
are extracted per document.

Applications, and thus documents, which share the same indexing information are grouped together into an
**application group**. Database **fields** are defined per application group, each with a name, data type
and additional indexing information. Every application is part of exactly one application group.

Information retrieval is decoupled from storage. A **folder** defines a search form through which users
can search for and  retrieve stored information. The folder can search across  one or multiple application
groups. Thus, the folder defines its own fields from an  extended set of field types. For each field, the
folder defines the set of operators (equal, greater than, less than, ...) and a mapping between the folder
field and the field in each application group.

## Documentum

All entities are represented as an **object**. An object is made up of a list of **attributes**.
Each attribute has a name, a data type, and can be single valued or repeating.

Every object is of exactly one type and has a unique ID.

One object can reference another object by storing the other objects ID in an attribute of the ID data type.

The type and the object ID are not represented as attributes.

## LiveLink

Every entity under management in Livelink is referred to as an **item**.

Each item is of exactly one **item type**. The item type defines a set of **system attributes** and
capabilities that define this item. For example, if items of this type can have content or if items
of this type can contain other items. In the later case,  the item type is named a **container**
item type.

**Custom attributes** are organised in **Categories**. Each item can be assigned to multiple categories.

Items of a container item type can contain other items. In this case, categories associated with the
container item are copied to items which are added to this container.

Internally, and  in  the API, items are called **nodes**. The item type is called **Node Subtype**.

It is not possible to create custom item types (node subtypes) through administration. Server extensions
can define new item types through content server SDK modules.

## OpenText Content Services (OTCS)

No notable changes. Containers are typically refered to as collections, but there are no fundamental
differences to Livelink containers.

## Sharepoint

Entities under management are referred to as **items**.

Every item belongs to exactly one **list**.

The list defines the **columns** that are available for each item in this list.

Every list is of exactly one type, which  can be a plain list or a document library. Technically, a document
library is just a list which stores  each file or folder in the document library as a row (item) in this
list. The folder hierarchy is built by a special parent refernce for each item.
The two list types are built-in and cannot be altered.

Later Sharepoint versions introduced **content types**. If content types are enabled for a document library,
each item (file or folder) in the document library must belong to exactly one content type. Once content
types are enabled in a document library, the set of available content types is defined per document library.

Content types are defined per site and can be reused across document libraries in the same site. Each
content type defines a set of columns.

## Alfresco

Information under management is stored in a graph of **nodes**. Each node has a list of properties, belongs
to exactly one **content type** and can have zero or more **aspects**.

The content type together with the aspects define the properties a node can have.

Every content type or aspect defines a list of properties with a qualified name, data type, cadinality, and
additional constraints.

Nodes are connected to a graph with **associations**. There are two types of associations. **Parent-child**
associations guarantee a loopfree single rooted tree. **Peer** associations be be between any two nodes.

Every type or aspect can define a set of associations between  nodes of this class as source and target nodes.
Each association type is identified by a qualified name and limits the nodes that be be set as target by their
content type or aspect. For example, nodes of type `cm:folder` can have associations of type `cm:contains`
that must have a node of type `cm:sysobject` as target (and the folder as source).

Single rooted graphs of nodes are organised in **stores**. There is only one store, `workspace://SpacesStore`,
which contains information from users. Other stores, like `system://system` contain internal information or are
deprecated  and just kept  for backward compatibility, like `workspace://lightWeightVersionStore`.

## Nuxeo

Every entity under manaement is referred to as a **document**, including folders.

Each document is of exactly one **document type**.

Document types are made up of a set of **Schemas** and a set of **Facets**.

Schemas are xml schema definitions (xsd) which define the fields of the document.

Facets are markers which define the capabilities of the document, and can contribute additional schemas.

Facets can also be applied dynamically to individual documents.

## OnBase

The atomic unit for Content under management is a **document**.

Each document is of exactly one **document type**.

The document type defines the **keywords** of a document, as well as additional capabilities and restrictions.

Keywords are defined globally, with a name and data type. When a keyword is associated with a document type,
it can be specified that this keyword is required or that this keyword needs a unique value across all documents of
this type.

In addition to documents, OnBase has the organisational concept of **folders**. Each folder is of exactly one
**folder type**. This type defines the keywords of each folder, as well as hierarchy and restrictions.

Keywords can be grouped into **keyword type groups**. These combine keywords and can be associated with
document types and folder types as a whole.

Keywords in a keyword type group can still be associated with document or folder types individually, as long as
the keyword type group is not associated with the type.
