# Data types

## FileNet Image Services

Index properties can only have one value.

Index properties can be optional or required.

## FileNet P8

All property types can be single-valued or multi-valued.

Multi-valued properties of the OBJECT data type are unordered sets of unique values (no duplicates).
Multi-valued properties of all other data types are ordered lists with duplicates allowed.

Individual elements of multi-valued properties must not be NULL.

P8 has a boolean flag "Value Required". If set for a property definition, NULL values are not  allowed in a single
valued property and empty sets (or NULL values) are not allowed in multi-valued properties.

## IBM Content Manager (ICM) aka DB2 Content Manager

Attributes are always single valued. Attributes can be placed in child components. These can have a minimum and
maximum cardinality.

Attributes can be required.

## Content Manager OnDemand (CMOD)

Database fields in an application group have always single cardinality, like a database.

Fields can be empty (null). It is not possible to mark a field as required in an application group. The reason
seems to be that the indexer which extracts fields from the print stream cannot guarantee that it can extract
always all fields. Since this is an automated process, documents that are detected in a print stream would
need to be discarded if a mandatory field is not found. It is possible to set a default value for fields in the
application definition.

## Documentum

Attributes can have a value or can be empty (i.e. NULL). It is not possible to define an attribute as required.

Every attribute can be defined as single value or as repeating (i.e. multi-valued) attribute. In the latter case,
values are organised as an ordered list. This list cannot contain NULL values.

## LiveLink

Attributes in a category can be marked as required. All attributes are multi-valued. It is possible to define a
default number of values and to mark the rows as "locked", which prevents the user from adding or removing
attribute rows.

## OpenText Content Services (OTCS)

No differences to Livelink

## Sharepoint

Column values are always single valued. Columns can have NULL values and can be required.

## Alfresco

Properties can be NULL. Properties cen be required and enforced required. In the first case, it is  the responsibility
of the UI to enforce this constraint. In the latter case,  the constrint is enforced by the backend repository.

Properties can be of single or multi cardinality.

## Nuxeo

Fields can be of single- or multi-valued.

Fields can be null and it is possible to define a field as required in the schema.

## OnBase

Keywords can be missing. It is possible to define that a keyword is required for a document type.

All keywords are inherently multi-valued.

If a keyword is part of a  keyword group, the entire keyword group is multi-valued rather than individual keywords in this group.