1. V1 Scope
Python only
static extraction only
no full runtime tracing
Kùzu used as projection layer
2. Node Types

For each node:

name
description
required fields
optional fields
example

Start with:

Project
Folder
File
Symbol
Feature
Responsibility
ConfigKey
DataEntity
ExternalDependency
Endpoint
3. Relationship Types

For each edge:

edge name
source node
target node
meaning
whether extracted, inferred, or derived

Core V1 edges:

HAS_FOLDER
HAS_FILE
DEFINES
IMPORTS
DEPENDS_ON
CALLS
IMPLEMENTS
BELONGS_TO_FEATURE
READS_CONFIG
READS_ENTITY
WRITES_ENTITY
USES_DEPENDENCY
ENTRYPOINT_TO
4. Extraction Source Rules

Define how each node/edge is created:

AST extracted
regex extracted
rule classified
AI classified
graph derived
5. Minimum Required Properties

This is important so V1 stays disciplined.

Example:

File

Required:

file_id
path
name
extension
language
file_type

Optional:

summary
line_count
is_entrypoint
6. IDs and Naming Rules

Define stable IDs now.

Examples:

file_id = sha1(path)
symbol_id = sha1(file_path + qualified_name + start_line)
feature_id = slug(feature_name)
7. Example Records

Show 1–2 full examples for:

one file node
one symbol node
one config node
one data entity node
one import edge
one call edge
8. V1 Excluded Items

Be explicit:

local variables
full AST trees
comments as standalone nodes
every string literal
every helper call unless meaningful
full control-flow graphs

That file will remove ambiguity before you build the extractor.
