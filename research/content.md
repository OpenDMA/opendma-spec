# Content / Binary  data  streams

## FileNet Image Services

A document is the logical entity. It can contain an ordered list of pages, each holding image or binary data.

Content is generally treated as immutable once committed to permanent storage.

## FileNet P8

A document is the logical entity. It can contain an ordered list of **content elements**. There  are two types
of content elements: **Content Refernce**, which holds the URL of an external object, and **Content Transfer**, which
provied access to a binary data stream.

## IBM Content Manager (ICM) aka DB2 Content Manager

Binary data is stored as ordered list of **item parts** of an item. The item type defines what part types are
allowed on items of this type.

## Content Manager OnDemand (CMOD)

OnDemand is optimised for the highly efficient storage of computer print output, primarily in the AFP format.
The indexing mechanism detects page breaks as well as document separations in the print stream. OnDemand stores
the entire print stream in a compressed format and keeps pointers into this print stream. These pointers
identify the start and end of each logical document within the print stream as well as resource items (fonts,
images, etc) in the AFP format which are used across multiple pages and documents in the print stream.

On request, OnDemand provides the binary AFP object stream for a document of  the print stream as well as the
referenced resource objects.

## Documentum

Each SysObject can be associated with a list of **Content** object. Each object holds the metadata of a binary
object associated with the document. This  metadata contains, among others, the page and format of the binary
data.

This forms a many-many relationsship: One document can be associated with multiple content objects, orderd by
their page number. One content object can be associated with multiple document objects to de-duplicate binary
data.

## LiveLink

A document node is associated  with a list of multiple versions. Each version stores one binary data stream.

## OpenText Content Services (OTCS)

Same content model as LiveLink.

## Sharepoint

In  a document library, each list item (which holds the metadata) is associated with exactly one File. A File
can have multiple versions. Each version is associated with exactly one binary data stream. 

## Alfresco

Alfresco has the special data type `d:content`. This allows for a very flexible use of binary content across
pre-defined but also custom content types.

The built-in content type `cm:content` has a sinhle-valued property named `content` of type `d:content`.

## Nuxeo

Nuxeo defines the basic xml data type `nxs:content` to handle binary data streams. In the standard files
schema, it combines an element named `file` of this type with  an element named ` filename` of type `xs:string`
into the complex type `file`.

## OnBase

A document is the logical entity. It can consist of  multiple  revisions. Each revision can have multiple
renditions. Each rendion can have multiple pages. If a rendition consists of exactly one "document" or
multiple pages depends on the document format.