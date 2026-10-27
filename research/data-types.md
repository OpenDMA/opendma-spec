# Data types

## FileNet Image Services

Index name constraints:

- 1 to 18 alphanumeric characters in any case
- no spaces and no special characters
- Custom index must not start with "F_"
- must not start with a, b, d, f, p, pv, q, t, w, x or X followed by a number
- must not start with "wfl_"

Index type:

- Numeric (decimal up to 30 characters; configured with a mask which defines decimal places or currency symbols)
- String (up to 239 characters)
- Date
- Menu (select from a list of possible values)

The "Menu" data type manages a list of pairs of a string display value and a one character return type. Return type can be letters, numbers and keyboard symbols.
There are up to 94 different return codes.

## FileNet P8

Property types:

- Binary
- Boolean
- DateTime
- Float (64 bit)
- ID (Microsoft GUID format)
- Integer (32 bit)
- Object (reference)
- String

It is strongly discourages to use "Float" for currency formats. Either use one integer field to store values in cents (e.g. 123 for 1$ 23ct) or use two
numeric fields, one for dollar and one for cents (other currenceis accordingly).

A "Choice List" holds a list of choices, which combine a display value with an  actual value. The actual value can only be of string or integer type.

## IBM Content Manager (ICM) aka DB2 Content Manager

Attribute types:

- Character, fixed length (character type: alphabetic, numeric, alphanumeric, extended alphanumeric, other)
- Character, variable length (character type: alphabetic, numeric, alphanumeric, extended alphanumeric, other)
- Short integer
- Long integer
- Decimal (total and fixed places)
- Double
- Date
- Time
- Timestamp
- BLOB
- CLOB
- Reference

## Content Manager OnDemand (CMOD)

Field Data Types:

- Small Int (signed 2 byte)
- Integer (signed 4 byte)
- Big Int (signed 8 byte)
- Decimal;  available as "DecFloat 16" and "DecFloat 34" with 16 or 34 digits of precision (only with DB2 backend)
- String; fixed or variable length; uper-, lower or mixed case
- Date
- Date/Time (date and time in 1 second increments)
- Date/Time (TZ) (like date/time, but converted to the clients timezone)
- Time (time of day in 3 second increments)

For a field of the String data type, it is possible to define a set of characters that will be removed from the string before storing or querying that field in the database.
There are three  distinct sets of up to 8 characters that will be removed as leading, trailing or embedded characters.

For all field types except decimal, it is possible to define a mapping between display strings and database values. 

Additional folder field types:

- Application Group
- Segment
- Text Search
- Annotation Text Search
- Annotation Color Search
- Document Type
- Hold

## Documentum

Attribute types:

- Boolean
- String
- Integer
- ID
- Time (Date and time)
- Double

## LiveLink

Attribute types:

- Text field
- Text multiline
- Text popup (selection list of predefined text values)
- Integer field
- Integer popup (selection list of predefined integer values)
- Date field
- Date popup (selection list of predefined date values)
- Flag checkbox (boolean)
- User field (reference to a user)
- Set

A **Set** is a list of one or more attributes grouped together.

## OpenText Content Services (OTCS)

OTCS supports all classic Livelink attribute types and additionally adds:

- Table Key Lookup
- Mutilingual Pop-up (with multilingual support)
- Multilingual Text Field
- Item reference

## Sharepoint

Column types:

- Single line of text
- Multiple lines of text
- Choice (menu of string values to choose from)
- Number (allows to specify number of decimal places between 0 and 5, can be "automatic")
- Currency (like "Number", but with an additional currency format; fixed currency symbol)
- Date and Time (can be limited to "Date only")
- Lookup (reference another list item; must specify the list and column to be shown)
- Yes/No
- Person or Group (can be limited to "People only")
- Hyperlink or Picture
- Calculated (outcome can be of types String, Number, Currency, DateTime or Boolean)
- Task Outcome (like Choice)
- External Data (like lookup, but against an external content type)
- Managed Metadata (select a term from a managed term set, allows multiple terms  to be selected)

## Alfresco

Property data types:

- d:any
- d:encrypted
- d:text
- d:mltext (multilingual text; map between locale and string)
- d:content
- d:int
- d:long
- d:float
- d:double
- d:date
- d:datetime
- d:boolean
- d:qname
- d:noderef (reference to another node by ID)
- d:childassocref
- d:assocref
- d:path
- d:category
- d:locale
- d:version
- d:period

As part of the constraints, a set of valid values can be defined for properties of the types
d:text, d:mltext, d:int, d:long, d:float, d:double. Technically, it is possible to define
a list of values for all data types.

It is possible to add custom data types as part of a data model.

## Nuxeo

Internally, Nuxeo stores primitive field values as one of these data types:

- String (Unicode)
- Long (signed 64-bit)
- Double (64-bit IEEE 754)
- Boolean
- Calendar (Date/time value with millisecond resolution)

Data modelling is based on XML Schema Definitions (sxd). Most primitive types defined in
`http://www.w3.org/2001/XMLSchema`, like `xs:string` or `xs:decimal`,  are mapped to one
of the internal field formats. This mapping can  cause data loss, e.g. precision when
`xs:decimal` is mapped to a Double or data range when `xsd:unsignedLong` is mapped to
a signed long. Some xsd primutive types, like `xsd:duration` do not have a mapping defined.

A complex field is made up of other fields, like a compex type in XSD.

## OnBase

Keyword Data Types:

- Numeric (9 digits)
- Numeric (20 digits)
- Date
- DateTime
- Currency
- Specific Currency
- Floating Point
- Alphanumeric