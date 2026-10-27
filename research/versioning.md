# Versioning

## FileNet Image Services

No native versioning mechanism.

## FileNet P8

Each document object represents one  distinct version of a document with its own metadata,
content elements and security.

Document objects representing different versions of the same document are organised through
a **Version Series**. This object holds a multi-valued reference property referencing all
versions of this object. Each object in this collection has a different version number.

FileNet supports a two level versioning numbering with a major version and a minor version.

The version series holds references to special objects in the series: the latest minor version,
the latest released version (which is the latest major version with minor version 0), and
the current reservation object.

A version series  can be "checked out". This creates a new document object at the head of
the version series as a copy of the latest version object. This  reservation object serves
as draft for a new version. Only the account which checked out the version series and
Administrators have access to this object. Metadata and content of this object can be updated
multiple times, until it is either checked in or discarded. Checking in this reservation
object converts it to a full version of the version series. The ID of this document object
is preserved.

## IBM Content Manager (ICM) aka DB2 Content Manager

ICM supports versioning at the item level, including metadata (components) and binary content
(parts).

Versioning policies are configured for the item type (never create, always create, prompt to
create). Versioning can be configured for the metadata and for each part type independently.

## Content Manager OnDemand (CMOD)

OnDemand does not support versioning.

## Documentum

Each document object represents one  distinct version of a document with its own metadata,
content elements and security.

Document objects representing different versions of the same document are organised through
the chronicle ID. All document objects with the same chronicle ID belong to the same logical
documents. Versions can form a tree, not just a linear list. Each individual object in this
version tree has a ID attribute pointing to the parent version this version is derived from.

There can be multiple "latest" versions, one in each branch of the version tree.

Each object in the version tree has a list of version labels. The first entry holds the system
generated implicit version label. All other positions contain symbolic version labels.

The versions form a single rooted tree. The chronicle ID is the object ID of the document object
at the root of this tree.

## LiveLink

Each document is uniquely identified by a document ID and is associated with multiple versions.
Each version has a version number. Each version is storing binary data and associated metadata,
like format and file size.

Custom attributes are stored in associated categories, not in the document. These can be versioned
independently. It is possible to create a new snapshot of category values with each version.

## OpenText Content Services (OTCS)

Identical to Livelink.

## Sharepoint

A list item can have a version history. It stores historic snapshots of the metadata as well
as the content.

## Alfresco

The versioning concept of Alfresco is different from the industry, which is a constant source
of confusion.

Alfresco knows a so called "live node" of each logical document. This is the node connected in
the node graph. If versioning is activated in the content type, the system creates a copy of
this node before it is updated. Versioning can be enabled for content, metadata or both updates.

Each node has a version label. This label gets updated by the system after a copy has been created
ands immediately before the metadata or content is updated.

The copies of nodes are stored in a separate node graph of a different store.

The ID of the live node remains unchanged, although its version changes. The latest version of a
node does not have its own persistent ID until the next version gets created.

A user can "check out" a document. This creates a new working copy. The owrking copy is only
accessible by the user who checked out  the document and  administrators. The working copy can be
checked-in or discarded. When cheked in, the metadata and contzent of the working copy are copied
over to the live node, replacing it and the working copy is deleted. The node ID of the working
copy does not survive the checkin.

## Nuxeo

Nuxeo has a Live Document, also called the Working Copy. Upon check-in, a new immutable archived
version is created from the working copy.

## OnBase

Each document can have a series of revisions. These contain multiple renditions, but no snapshots
of historic metadata.