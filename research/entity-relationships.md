# Entity Relationships

## FileNet Image Services

FileNet IS supports folder/document containment but lacks general-purpose, user-defined document relationships.

## FileNet P8

The data model has the special "reference" data type. This allows an element to reference another object
in the same scope (e.g. an object store).

There are some explicit relationship object, like `ReferentialContainmentRelationship`, which use
this reference data type to record references to a source and a target object.

## IBM Content Manager (ICM) aka DB2 Content Manager

The data model has the special "reference" data type. Attributes of this type contain  a reference to
another item.

ICM supports directed **links**. Each link object is of exactly one **link type**. The link object
holds a reference to a source item, a target item, the link type name and an optional **link item**
which can hold additional metadata.


## Content Manager OnDemand (CMOD)

OnDemand does not record relationships between documents. These need to be managed outside of CMOD.

## Documentum

Documentum uses the ID data type ti store the unique identifier of other obejcts.

Documentum has the `dm_relation` type representing a directed relationship between two objects by
recording the  IDs of the source and target objects.

## LiveLink

There is no general purpose realtionship model in Livelink.

Folder containment is implemented directly. An item has exactly one parent, referenced by numeric item ID. 

## OpenText Content Services (OTCS)

OTCS has added the  reference attribute type.

## Sharepoint

Sharepoint has the "Lookup" data type. Columns of this type reference items in lists within the same site.

These reference data types are only used for custom user defined metadata. Internal structures like
version history or file/folder containment are not designed using these data types.

## Alfresco

Alfresco explicitly models **associations** between nodes. Thse form the directed edges in the node graph.

There are two types of associations: **Parent-child associations** and **peer associations**.
Parent-child associations form a single-rooted loop free tree. Peer associations can be between any two
nodes.

The content type of a node defines all possible outgoing associations for nodes of this type. Associations
are identified by a qualified name.

Nodes can be the target of multiple parent-child associations. In this case, one of them is the designated
**primary parent association**.

## Nuxeo

Documents  can be referenced by UUID or path. A `documentResolver` can be attached to a normal String field,
validating that the content is a UUID  or  path pointing to a valid existing object.

General purpose directed relationships are stored as special repository objects, such as `Relationship`
documents.

Nuxeo natively manages folder hierarchy. Every *placeful* document has exactly one parent.

## OnBase

OnBase has neither reference keyword types nor does it have a general purpose link mechanism.

It is possible to configure document cross-reference, which is dynamically resolved by keyword value.

The folder hierarchy is built-in and not based on relationships between objects.