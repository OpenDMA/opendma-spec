# Data modelling

## FileNet Image Services

Index fields are defined as a flat list.

Document classes are defined as a flat list with no inheritance, grouping or other relationship between them.

Index fields are associated with document classes.

Data modelling is not available as part of the entity model.

## FileNet P8

A **Class Description** together with multiple **Property Descriptions** are an immutable snapshot
describing the properties, capabilities and constraints of live objects.

To keep the system in a consistent state while modifying the data model, P8 has a dictinct second
set of class metadata: **Class Definitions** and **Property Definitions**.

Administrator can make modifications to these definitions while the descriptions still show the
current state. After the new state of definitions is persisted, the system activates the new
class shape and eventually updates the descriptions.

Definitions only exist for customisable classes. Static built-in classes do not have paired
definition objects.

Classes have exactly one superclass, from which they inherit all properties. The only exception
is the root of the class hierarchy, the `GenericObject`. This forms a single rooted loop free
tree.

Sub classes can differ from their superclass in these ways:
- Additional properties can be added
- Default values of properties can be different
- An inherited property which is optional in the superclass can be mandatory in the subclass
- Allowable values can be further constrainted in the subclass, but not the other way around

Note: We have seen the above constraints being violated in built-in system classes.

If a class is customisable, like the `Document` class, then all sub classes are customisable as
well. They form a branch within the global class hierarchy.

The class *definition* of this first customisable class of this branch, like `Document`, does
not have a parent class definition, while the corresponding class description does have a
super class description.

```text
Class Descriptions:           Class Definitions:
-------------------           ------------------
  Generic Object
    Subscribable
      Containable                 Document
        Versionable                  FooDocument
          Document                   BarDocument
            FooDocument
            BarDocument
```

P8 uses Object Stores as isolation boundary for data. Each object store has its own data model and
security. Consequently, each object store has its own set of class definitions and property
definitions as well as its own class hierarchy.

Some entities, like marking sets, are defined globally for the entire enterprise across multiple
object stores. These objects live in the P8 domain. This domain has again its own  class hierarchy.

Class and Property Descriptions as well as Class and Property Definitions are all obejcts
themselves. The class and properties of an object can be inspected by reading its `ClassDescription`
property and then inspecting the class description.

Data modelling is available within the data model via the class and property definition objects.

## IBM Content Manager (ICM) aka DB2 Content Manager

Item types are defined as a flat list with no inheritance, grouping or other relationship between them.

Part types are built-in system defined and cannot be customised.

Attributes are defined globally are are then associated with item types.

Components are defined per item type and are not shared between item types.

Data modelling is not available within the data model. For example, an item type is not accessible
as an item itself.

## Content Manager OnDemand (CMOD)

Application groups are defined as a flat list with no inheritance, grouping or other relationship between them.

The same is true for applications within these application groups and for folders.

Data modelling itself is not available within the data model.

## Documentum

A type *can* have a super type, but it does not have to. Documentum does not force all types
into a single rooted class hierarchy.

If a type has a super type, it inherits all attributes defined for this super type verbatim.
It is not possible to alter inherited attributes in any way.

Types are described within the data model with objects of the type `dm_type`. It references
an optional super type by the text `super_name`. Attributes are described with the multi
valued properties `attr_name`, `attr_type` and `attr_repeating`. 

## LiveLink

The "Node Subtype" aka "Item Type" is system defined and built-in. It cannot be modified.

Node subtypes are effectively a flat set of type identifiers at the data-model level.

Categories are user defined and used for custom data modelling. Category definitions themselves
are stored in the system as items of sub type "Category". They can be stored anywhere
in the system, but are typically stored in the Categories Volume (Volume ID 133). 

## OpenText Content Services (OTCS)

No notable changes to Livelink.

## Sharepoint

Lists are defined independently per site. There is no inheritance between lists.

Content Types must define a super type from which they inherit columns. They form a single rooted
tree with the `Item` as common ancestor.

Content Types are defined per site collection, thus there is a individual content type hierarchy
per site collection.

When a content type is assigned to a list, the items in this list which use this content ytpe
carry fields from the content type as well as the columns defined for the list. To support
this, Sharepoint creates an individual copy of the content type per list.

Data modelling is available as a separate API, but it is not part of the information under management.

## Alfresco

Content Types must have another content type as super type from which they inherit properties and associations.
They form a single rooted hierarchy.

Aspects can have another aspect as super aspect, but they don't have to.

Content types can define a set of aspects as mandatory aspects. Nodes of this content type get these
aspects applied automatically.

Content types and aspects are not part of the node graph. They are defined in XML files, which are
typically bundled with server extensions deployed as AMP (Alfresco Module package) archives.

Related content types and aspects are bundeled together into "models".

Alfresco allows dynamic definition of models by loading model XML files from a dedicated location
in the repository.

## Nuxeo

Schemas, document types and facets are defined in XML files, which are typically deployed as part
of Nuxeo extensions (typically OSGi bundles).

## OnBase

Document types are defined as a flat list with no inheritance or other relationship between them.
They can be grouped into document type groups, which has no implications on the data model itself.
Groups are mainly organisational to structure the display of document types in the UI.

Keyword types are defined globally and are associated with  document types and folder types.

Data modelling happens outside of the data model, i.e. there is no representation of these types
as documents.
