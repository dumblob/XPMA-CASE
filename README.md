# XPMA CASE

*CASE done right!*

Computer Aided Software Engineering tool implementing the *XPM combined model*. XPM ties together BPMN 2.0 (process modeling) and CSDDM 1.0 (data modeling) and provides additional convenient views of the resulting model (e.g. Entity Relationship Diagram, Use Case Diagram). XPMA CASE produces input for [XPMA](https://github.com/dumblob/XPMA).

## Complex System Data Definition Model 1.0

<!--
Complex data modeling
comprehensive data modeling
attribute data modeling
enhanced data modeling
? DDL
data life cycle modeling
enhanceable data definition ???
eXtensible Data Definition Model
+eXtensible System Data Definition Model
+System Data Definition Model
Complex Data Definition Model
+Logical Data Definition Model
unified data definition Specification
deep data definition model
system data modeling
data system modeling
complex system data modeling
complex data system modeling
system data definition language
+system data definition notation
-system data and properties definition
table data definition language
tree data definition language
extensible system data definition
system data extensible definition
-->

CSDDM 1.0 is a new model allowing formally, throughly, but still concisely describe data with properties from the top-most abstraction level - from the user interface. It is designed with coupling with process modeling in mind.

The main goal of CSDDM is to define firm detailed boundaries for implementation of complex systems (because these are nowadays implementation-technology-dependent and thus a data model implemented in two different technologies results in totally different solutions, often strongly compromising the outcome quality, because the decision of settings of many properties is left to implementators who have no overview of the whole system and are strongly led astray by significant limitations of the chosen implementation technology - imagine Java, SQL, etc.).

The model is structured as a **tree of items** (nodes and leafs), whereas each item has it's own set of **properties**. The model is depicted in a form of a table for better understandability. Table depiction is also the recommended one.

*Task X*

kind | nest | id | perm | constr | init | sync | consis | quant | imp
---  | ---  | ---| ---  | ---    | ---  | ---  | ---    | ---   | ---
c | 0 | collection00 | add_rem | - | - | hardlink | best-effort | 0 | medium
a | 1 |     attr00 | no_rw | string_ascii_printable | "" | hardlink | best-effort | 0 | medium
a | 1 |     attr01 | write | integer | 0 | hardlink | best-effort | 0 | medium
c | 1 |     collection00 | add_rem | - | - | hardlink | best-effort | 0 | medium
c | 0 | collection01 | add | - | - | hardlink | best-effort | 0 | medium
a | 1 |     attr00 | read | string_ascii_printable | "" | hardlink | best-effort | 0 | medium
a | 1 |     attr01 | read | string_ascii_printable | "" | hardlink | best-effort | 0 | medium
c | 1 |     collection02 | rem | - | - | hardlink | best-effort | 0 | medium
a | 2 |         attr00 | write | string_ascii_printable | "" | hardlink | best-effort | 0 | medium
c | 1 |     collection03 | add | - | - | hardlink | best-effort | 0 | medium
a | 2 |         attr00 | write | string_ascii_printable | "" | hardlink | best-effort | 0 | medium
a | 2 |         attr01 | no_rw | string_ascii_printable | "" | hardlink | best-effort | 0 | medium

*Task Y (referencing content of Task X)*

kind | nest | id | perm | constr | init | sync | consis | quant | imp
---  | ---  | ---| ---  | ---    | ---  | ---  | ---    | ---   | ---
c | 0 | collection05 | add_rem | - | - | hardlink | loose | 0 | medium
a | 1 |     attr00 | write | string_ascii_printable | "" | hardlink | best-effort | 0 | medium
a | 1 |     .collection00.attr01 | write | integer | 0 | hardlink | best-effort | 0 | medium
a | 1 |     .collection01.collection02.attr00 | read | integer | 0 | hardlink | loose | 0 | medium
c | 1 |     .collection01.collection03 | rem | - | - | hardlink | loose | 0 | medium

*Collection and attribute life cycle*

![c_a_life_cycle](doc/thesis/fig/life_cycle-attribute.svg)

### Terminology

**document** is a logical data volume (can be either structured or
unstructured)

**collection** is a structural description of a collection of attributes

**attribute** is a structural description of a document

**record** is a collection or an attribute

### Legend

**kind**

- designates whether the record is a collection or an attribute

**nest** (nesting depth)

- integer designating how much the record is nested in the global namespace

**id** (identifier)

- \[\_A-Za-z\]\[\_A-Za-z0-9\]\*
- is case-sensitive
- aliases are not supported (on purpose - to stay KISS)
- note it's only ASCII (no UTF-8 support because of compatibility)
- is global for records with(so it can appear multiple times in different BPMN 2.0 Data Objects and BPMN 2.0 Gateways)
    - it's recommended to think through labeling strategy for identifiers first (imagine modelling SAP)
- it represents a collection or attribute name
- it's compatible with SQL names and URIs
- referring to an existing record schema
    - syntax for external record schemas
        - URI pointing to an external API compatible with XPM (the URI must start with „*\<schema\>:*“)
            - the URI API must support parameter passing (credentials, settings for fetching, security stuff, etc.)
            - constraints set etc. shall be provided by the external schema
    - syntax for local record schemas
        - top-down (direction from the tree root), use dot as a separator in full path to the referred record schema (full path starts with a process ID, which might be omitted to refer to the current process where this full path is used, and continues with a dot followed by a record with)
        - bottom-up (direction from the current record), use colon („double dot“) as a reference to a parent in relative path to the referred record schema (to refer to self, use „*:nameOfTheCurrentRecord.*“)
        - both segment separators (dots and colons) can be arbitrarily used in one path
    - note, that reference paths contain incompatible characters e.g. with SQL
    - a reference collection can't have any additional nor any less child attributes nor child collections
    - attributes (they're not visible) of a reference collection shall have the same kind, permissions, constraints set, and initial value as the referenced collection
    - note this referencing doesn't say anything about the data the schema describes as referencing just mimics the referred record schema (deep copy of the schema)
    - recursion (both direct and indirect) is allowed (thus it's easy to model e.g. tree structures – even infinitely nested ones)
        - FIXME there must be some way to stop the recursion (stopping condition like NULL/NIL/NONE/...)
    - note, that arbitrary graph data can be modeled

**perm** (permissions)

- collection record permissions
    - no\_add\_no\_rem/add/rem/add\_rem
    - options are mutually exclusive
- attr permissions
    - no\_rw/read/write
    - options are mutually exclusive
    - warn in case this role/person has on this attribute \`no\_read\_no\_write\` or \`read\` and some of the parent collections are rem/add\_rem
    - warn in case someone added a new attribute to the table/document view, because it's by default \`no\_read\_no\_write\` and not used anywhere

**init** (initial value)

- in compliance with the chosen constraint
- constant (e.g. some number) or dynamic (e.g. current date and time)
- short turing-complete definition (preferrably a readable existing programming language) returning a constraint-satisfying value (the definition shall be a formal notation of the algorithm, but not necessarily a fully functional program)

**constr** (constraints set)

- examples
    - none (plain data of arbitrary length)
    - string\_utf8/string\_ascii\_printable/... (arbitrary length)
    - bool/int32/int64/int\_big/float64/float128/float\_big/...
    - time (supporting dates starting at least in 2000)
    - PDF/CSV/JSON/XML/HTML/MP3/JPG/PNG/... (totally specific types; effort shall be invested into making these types version agnostic – so no HTML4 nor HTML5 …)
    - FIXME: APP (an application to be executed and possibly visually embedded)
    - FIXME: eval (constraints\_set which guarantees COW-like execution changing the DB schema on success – writeable data become temporarily read-only; could be used also as a glue for special occasions like modelling infinite-recursive structures, arbitrary trees, etc.)
        - shall maintain the consistent part of the DB schema (i.e. attributes with full in the *consis* field) consistent all the time
        - problem of having description of the new state and/or transitional steps
            - for schema changes, steps are required (e.g. to model a model using the model itself)
            - for read-only stuff (e.g. recursion), steps are inconvenient and just the end state description is preferred
- it shall be easy to add a new constraint set
- a survey finding out which types exist across most used and perspective DBs (to provide basis for definition of a minimal set of optimized types to avoid stored procedures etc.) is planned
- each set defined as
    - unique label (there will be public repos easily addable in case no conflicting labels are present)
        - guidelines for recommended naming
    - version (semantic versioning with backward-compatibility extension: <https://github.com/mojombo/semver/issues/163> )
    - description how to visually interpret the contained value (serves also as a hint how to auto-construct UI); shall provide at least
        - expected amount of the value data visible at the same time
            - none/small/medium/large/full
            - e.g. string with a name will be smaller than string with a book content, picture or movie makes sense only if it's fully visible
    - default initial value
    - short turing-complete pseudo-code (preferrably a readable existing programming language) checking correctness on input and output from the DB (the code shall be only a formal notation of the check, but not necessarily a fully functional program)
        - this code shall use a general (platform agnostic) stream interface (disassembling into blocks of data similar to, but more efficient than, POSIX shell pipe streams)
        - in case the code is written in a real programming language and is in accordance with that particular language specification (including also thread-safety etc.), it shall be directly executable on a system, which that particular language supports (e.g. has installed)

**sync** (synchronization to a referenced record)

- once/copy/cache/hardlink
- once – a deep copy of a newly added value, but only once without any further synchronization
- copy – a deep copy, fully synchronized with the referenced record
- cache – a deep copy, fully synchronized with the referenced record once in a while (the while is implementation specific, but must assure, that the recency of the copy meets end-user expectations)
- hardlink – a plain reference to the data in the referenced record; this is the default also for regular (non-reference) records

**consis** (consistency level during parallel user access)

- FIXME full/best-effort/loose/none
- *best-effort* is a guaranteed immediate queuing of requests which is consistent if the throughput of the DB is higher than the load for a small fraction of time (the fraction varies)
- *loose* does guarantee consistency only at certain time points (e.g. periodically every midnight a consistency making operation is run; or just randomly when the administrator decides to run a consistency making operation manually)
- *none* does guarantee, that the consistency won't be reassured at any time (in other words, it'll be copied only once and only once at the beginning during the whole lifetime)
- a collection itself has the lowest consistency level of each of it's non-reference attributes or subcollections
- a reference to a collection or attribute can have the same or worse consistency level

**quant** (quantity)

- 0..N
- expected number of items of this record
- 0 means „unknown“

**imp** (importance)

- very\_low/low/medium/high/very\_heigh
- expected importance in the context of all other records used in this particular BPMN Task
- useful for auto caching, UI auto construction, etc.

### Remarks

- CRUD split among collection and attributes (CRD on collections; RU on attributes)
    - problem of grouping attributes into collections
        - because CRUD builds on SQL syntax and semantics, which in turn is not consistent (a mixture between pure relational ordering, non-relational ordering supporting ordering and non-relational single-value outputs)
- relationship to USE-CASE diagrams: USE-CASE is a bunch of processes operating with the same data
- BPMN 2.0 weakness – it seems, it doesn't support workers interchange tasks
- Unique combination of a ROLE + BPMN TASK + STARTING STATE (highest permission – warn in case of inconsistency)
- modelling of legislation (law acts) as processes with data
- specify, that the particular attribute/collection schema should not be visualized, although it's rw
    - important for large collection schemas (tens and more attributes and collections), for dashboards, etc.
    - derive from operational statistics (e.g. recording of user feedback in UI – „has manually hidden attribute X in a table showing collection Y“)

### Rules for combinations of properties

*Deny everything, allow a few!*

The following set of rules describes all possible valid combinations of tree item properties settings. Different backend engines implementing CSDDM can though provide their own set of rules matching capabilities of the particular engine in case the engine does not fully support the default set of rules.

<!--
Each set of rules has it's unique identifier having exactly 128 8-bit characters mathing [ERE](https://en.wikipedia.org/wiki/Regular_expression#POSIX_extended ) `^[A-Za-z0-9]{128}$` with each character being randomly generated.
-->

The following rules are sorted according to their priority (highest at the top). Priority is used for default settings (e.g. when adding a new node or leaf in a modeling program).

kind | nest | id | perm | constr | init | sync | consis | quant | imp
---  | ---  | ---| ---  | ---    | ---  | ---  | ---    | ---   | ---
c | 0 | * | add_rem | - | - | hardlink | loose | 0 | medium
a | 1 | * | no_rw | string_ascii_printable | "" | hardlink | best-effort | 0 | medium

## Remarks

* SSADM (entity life cycle) - weighting of the process and data modeling (eighties)
