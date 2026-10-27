# title goes here

## FileNet Image Services

FileNet IS maintains a folder hierarchy. Each folder has a name and can have at most
one parent folder. Folders without a parent are root-level folders.

Documents can belong to no, one, or multiple folders.

Users can create folders and  add documents to folders in the  desktop applications.

## FileNet P8

P8 provides a file system made up of `Folder` objects. Each folder object has exactly
one parent, except for the root folder. Each object store has a dedicated root folder
and thus a single folder hierarchy. The system guarantees a loop free tree.

The `Folder` class can be subclassed and extended with custom properties.

Objects (documents, custom objects, ...) are linked into folders with relationship
objects, e.g. the `ReferentialContainmentRelationship`.  This is a dynamic relationship.
It stores a reference to the containing folder, a reference to a version series and
instructions what element to choose from a version series. For example, it is
possible to create a RCR which represents the most recent minor version or the most
recent major version of an object.

## IBM Content Manager (ICM) aka DB2 Content Manager

The predefined `DKFolder` link type creates a directed relationship between two items.

Any Item Type can be used to create folders, regardless of whether it represents a document,
resource or ordinary Item.

There is no guarantee for a loop free tree.

Folder behavior is activated by the Item's semantic type (DK_ICM_SEMANTIC_TYPE_FOLDER).

An item  can belong to multiple folders and a folder can contain multiple items.

Newer versions of ICM have introduced a hierarchical Item Types which enforces single-parent containment.

## Content Manager OnDemand (CMOD)

OnDemand does not provide any file system hierarchy.

There are "folders", which must not be confused with folders in a file system. Instead,
these are search templates. "Cabinets" then organise multiple folders.  

## Documentum

Every object of type `SysObject` (or one of its decendant types) can be contained in zero,
one, or multiple folders. This is managed by the repeating attribute `i_parent_id`.

The system guarantees that the folder hierarchy forms a single rooted loop free tree.

The root of the folder hierarchy is a cabinet, which is a specialised folder type.

## LiveLink

The folder hierarchy is a native, single-parent tree structure.

Folders are nodes of subtype `0`. Every node has exactly one parent. Containment is a
system-managed structural relationship.

Not only Folder (0), but also other subtypes such as Projects and Business Workspaces
can contain nodes.

Volumes (aka Workspaces) form the roots of folder hierarchies.

## OpenText Content Services (OTCS)

No structural changes to Livelink, but the data  structures have been optimised.

## Sharepoint

A document library in Sharepoint maintains a Windows-like filesystem hierarchy. It is
intentionally designed Windows-like to allow Sharepoint document libraries to be
mounted (i.e. mapped) into a windows file system.

The filesystem is system maintained and is not based on generic Sharepoint functionality.

## Alfresco

The folder hierarchy is directly represented in the node graph. The built-in basic content model
defines `cm:content` and `cm:folder` types as decendants of `cm:cmobject`. For `cm:folder`, it
defines a `cm:contains` parent child association as a directed edge between a folder node and
any `sdys:base` node.

## Nuxeo

The folder hierarchy is a native, single-parent tree structure. Folders themselves are documents.
The folder behaviour is controlled by the `Folderish` facet.

Each document has a system-managed parent reference.

A document can have at most one parent. Documenmts without parent are referred to as *placeless* documents.

Nuxeo does not support multiple parents, but offers **proxies** to achive similar capabilities.
A proxy is a document which solely holds the reference of another documenmt.

## OnBase

OnBase supports folder hierarchies, but they differ from traditional file-system hierarchies.

A folder in onbase has exactly one parent, except for the root. Folders form a loop free single
rooted tree.

Every folder object is of exactly one folder type. The folder type defines the keywords, folder
content, and sub folder restrictions.

Folders can be be static, dynamic, or both - defined by the folder type. The content of a dynamic
folder is based on a keyword search. The content of a static folder is manually added by users.

A folder type can have sub folder types. This defines the folder hierarchy, not any inheritance.
Sub folder types (read "sub-folder types") restrict the folder types of the folder objects that
are contained in folders of this type.

A document can be contained in none, one or multiple folders.

The root of a folder hierarchy is a cabinet. Each cabinet is of exactly one folder type.