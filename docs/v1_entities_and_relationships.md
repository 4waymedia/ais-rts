V1 Entities and Relationships
1. V1 Scope

AIS-RTS V1 is designed to create a compact, structured relationship model for a Python codebase. The first version should remain narrow, practical, and easy to implement.

V1 boundaries
Python only
static extraction only
no runtime instrumentation
no live execution tracing
no full control-flow graph
no language-server dependency required
Kùzu used as a graph projection layer
canonical metadata remains the durable source of truth
V1 objectives
identify the core files in a project
classify their roles
extract symbols such as classes and functions
detect imports and major dependencies
identify config keys and basic data entities
connect files and symbols into a traversable graph
support compact scope responses for AI-assisted coding
2. Design Principle

Track only the relationships that improve change understanding.

V1 should focus on relationships that help answer:

what this file does
what it depends on
what defines the behavior
what configs or entities influence it
what other files are likely affected

The goal is not to mirror the full AST.
The goal is to create a small, high-value model for reasoning.

3. Node Types
3.1 Project

Represents the top-level repository or application being analyzed.

Description

A single root codebase or application boundary.

Required fields
project_id
name
root_path
primary_language
Optional fields
description
repo_url
default_branch
Example
{
  "project_id": "proj_ais_rts",
  "name": "ais-rts",
  "root_path": "/Users/paul/code/ais-rts",
  "primary_language": "python",
  "description": "AI Scope Relationship Tracking System"
}
3.2 Folder

Represents a directory in the repository.

Description

Used to preserve the structural hierarchy of the project.

Required fields
folder_id
path
name
Optional fields
parent_path
Example
{
  "folder_id": "fld_src_extractors",
  "path": "src/extractors",
  "name": "extractors",
  "parent_path": "src"
}
3.3 File

Represents a source file or other important project file.

Description

A file is one of the primary units AI must reason about during scope selection.

Required fields
file_id
path
name
extension
language
file_type
Optional fields
size_bytes
line_count
hash
summary
is_entrypoint
file_type examples
class_module
service
helper
config
route
worker
schema
test
model
cli
prompt
sql
component
Example
{
  "file_id": "file_backend_core_outline_manager_py",
  "path": "backend/core/outline_manager.py",
  "name": "outline_manager.py",
  "extension": ".py",
  "language": "python",
  "file_type": "service",
  "line_count": 420,
  "is_entrypoint": false,
  "summary": "Builds and saves structured outlines from transcripts."
}
3.4 Symbol

Represents a defined code unit.

Description

A symbol is a named code object that matters for understanding behavior.

V1 supported symbol types
class
function
method
constant
route_handler
task_runner
Required fields
symbol_id
file_path
name
qualified_name
symbol_type
start_line
end_line
Optional fields
signature
visibility
docstring_summary
responsibility
is_entrypoint
Example
{
  "symbol_id": "sym_outline_manager",
  "file_path": "backend/core/outline_manager.py",
  "name": "OutlineManager",
  "qualified_name": "backend.core.outline_manager.OutlineManager",
  "symbol_type": "class",
  "start_line": 10,
  "end_line": 420,
  "signature": "class OutlineManager",
  "docstring_summary": "Builds and saves structured outlines from transcripts.",
  "is_entrypoint": false
}
3.5 Feature

Represents a product capability, system capability, or functional area.

Description

Used to map files and symbols to meaningful business or system functions.

Required fields
feature_id
name
slug
Optional fields
description
category
Example
{
  "feature_id": "feat_outline_generation",
  "name": "Outline Generation",
  "slug": "outline_generation",
  "category": "content_pipeline",
  "description": "Builds structured outlines from transcript data."
}
3.6 Responsibility

Represents the role or purpose of a file or symbol.

Description

This is a semantic label, not a raw extracted object.

Required fields
responsibility_id
name
Optional fields
description
Example
{
  "responsibility_id": "resp_queue_processing",
  "name": "queue_processing",
  "description": "Manages queue creation, execution, and status handling."
}
3.7 ConfigKey

Represents a configuration key or environment variable.

Description

Used to connect behavior to configuration influence.

Required fields
config_key_id
name
source_type
Optional fields
default_value
description
source_type examples
env_var
json_config
yaml_config
python_constant
Example
{
  "config_key_id": "cfg_debug",
  "name": "DEBUG",
  "source_type": "env_var",
  "default_value": "false",
  "description": "Controls debug behavior for the application."
}
3.8 DataEntity

Represents a database table, queue object, model, or structured data object.

Description

Used to connect files and symbols to the data they read or write.

Required fields
data_entity_id
name
entity_type
Optional fields
description
entity_type examples
table
view
queue_record
json_object
cache_key
Example
{
  "data_entity_id": "de_video_outlines",
  "name": "video_outlines",
  "entity_type": "table",
  "description": "Stores saved outline artifacts for videos."
}
3.9 ExternalDependency

Represents an imported library, framework, package, or service.

Description

Used to track external code or system dependencies used by a symbol or file.

Required fields
dependency_id
name
dependency_type
Optional fields
version
description
dependency_type examples
python_package
framework
api_service
db_driver
Example
{
  "dependency_id": "dep_fastapi",
  "name": "fastapi",
  "dependency_type": "framework",
  "description": "FastAPI web framework"
}
3.10 Endpoint

Represents an entrypoint into code behavior.

Description

Used for routes, CLI commands, workers, and other execution starting points.

Required fields
endpoint_id
name
endpoint_type
identifier
Optional fields
description
endpoint_type examples
api_route
cli_command
worker_job
scheduled_task
Example
{
  "endpoint_id": "ep_cli_full_chapter_enrichment",
  "name": "Full Chapter Enrichment CLI",
  "endpoint_type": "cli_command",
  "identifier": "python3 cli/test_full_chapter_enrichment.py ID --all",
  "description": "Runs chapter enrichment for a given video."
}
4. Relationship Types

Each relationship below should define:

source node
target node
meaning
origin type

Origin types:

extracted
inferred
derived
4.1 HAS_FOLDER
Source

Project

Target

Folder

Meaning

The project contains this folder.

Origin

extracted

4.2 HAS_FILE
Source

Folder

Target

File

Meaning

The folder contains this file.

Origin

extracted

4.3 DEFINES
Source

File

Target

Symbol

Meaning

The file defines this symbol.

Origin

extracted

4.4 IMPORTS
Source

File

Target

File or ExternalDependency

Meaning

The file imports another internal file or an external dependency.

Origin

extracted

Note

V1 may first store raw imports as strings, then resolve them to internal files where possible.

4.5 DEPENDS_ON
Source

File

Target

File

Meaning

The file has a meaningful dependency on another internal file.

Origin

derived

Note

This may be derived from resolved imports, symbol calls, shared config use, or shared data entity coupling.

4.6 CALLS
Source

Symbol

Target

Symbol

Meaning

One symbol directly calls or references another symbol.

Origin

extracted or derived

Note

V1 can keep this limited to cases that can be reliably detected.

4.7 IMPLEMENTS
Source

File or Symbol

Target

Responsibility

Meaning

The file or symbol serves this responsibility.

Origin

inferred

4.8 BELONGS_TO_FEATURE
Source

File or Symbol

Target

Feature

Meaning

The file or symbol is part of this feature area.

Origin

inferred

4.9 READS_CONFIG
Source

File or Symbol

Target

ConfigKey

Meaning

The file or symbol reads or references this config key.

Origin

extracted

4.10 READS_ENTITY
Source

File or Symbol

Target

DataEntity

Meaning

The file or symbol reads from this data entity.

Origin

extracted or inferred

4.11 WRITES_ENTITY
Source

File or Symbol

Target

DataEntity

Meaning

The file or symbol writes to this data entity.

Origin

extracted or inferred

4.12 USES_DEPENDENCY
Source

File or Symbol

Target

ExternalDependency

Meaning

The file or symbol uses an external dependency.

Origin

extracted

4.13 ENTRYPOINT_TO
Source

Endpoint

Target

Symbol

Meaning

This endpoint begins execution in or hands control to this symbol.

Origin

extracted or inferred

4.14 LIKELY_IMPACTS
Source

File or Symbol

Target

File or Symbol

Meaning

A change here is likely to affect the target.

Origin

derived

Note

This relationship should be derived conservatively from:

imports
calls
shared data entities
shared config keys
entrypoint adjacency
5. Extraction Source Rules

This section defines how nodes and relationships are created.

5.1 AST extracted

Use Python ast for:

module imports
classes
functions
methods
decorators
constants
line ranges
docstrings where available
Generates
File
Symbol
DEFINES
some IMPORTS
some CALLS
5.2 Regex extracted

Use targeted regex for:

os.getenv(...)
os.environ[...]
common config access patterns
SQL table name mentions
CLI command definitions
route decorators
framework-specific entrypoint hints
Generates
ConfigKey
some DataEntity
some Endpoint
some READS_CONFIG
5.3 Rule classified

Use deterministic path and naming rules for:

file_type
is_entrypoint
likely file role
likely endpoint role
Examples
path includes /routes/ → file_type = route
path includes /workers/ → file_type = worker
path includes /cli/ → file_type = cli
name includes config → file_type = config
class name ends with Manager → likely service/orchestrator
5.4 AI classified

Use AI only for semantic labels that are difficult to assign deterministically.

Good AI classification targets
file summary
responsibility label
feature mapping
change significance
review priority
Generates
Responsibility
Feature
IMPLEMENTS
BELONGS_TO_FEATURE
5.5 Graph derived

Generate these only after canonical metadata exists.

Good derived targets
DEPENDS_ON
LIKELY_IMPACTS
resolved internal import links
transitive scope hints
6. Minimum Required Properties

This section defines the minimum fields V1 must produce.

6.1 Project

Required:

project_id
name
root_path
primary_language
6.2 Folder

Required:

folder_id
path
name
6.3 File

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
hash
6.4 Symbol

Required:

symbol_id
file_path
name
qualified_name
symbol_type
start_line
end_line

Optional:

signature
docstring_summary
responsibility
is_entrypoint
6.5 Feature

Required:

feature_id
name
slug

Optional:

description
category
6.6 Responsibility

Required:

responsibility_id
name

Optional:

description
6.7 ConfigKey

Required:

config_key_id
name
source_type

Optional:

default_value
description
6.8 DataEntity

Required:

data_entity_id
name
entity_type

Optional:

description
6.9 ExternalDependency

Required:

dependency_id
name
dependency_type

Optional:

version
description
6.10 Endpoint

Required:

endpoint_id
name
endpoint_type
identifier

Optional:

description
7. ID and Naming Rules

V1 should use stable IDs so extracted data can be rebuilt consistently.

7.1 Project IDs

Use a stable slug:

project_id = "proj_" + slug(project_name)

Example:

proj_ais_rts
7.2 Folder IDs

Use normalized path:

folder_id = "fld_" + normalized_path

Example:

fld_src_extractors
7.3 File IDs

Use normalized relative path:

file_id = "file_" + normalized_path

or hashed form:

sha1(relative_path)

Readable IDs are better in V1 for debugging.

Example:

file_backend_core_outline_manager_py
7.4 Symbol IDs

Use:

file path
qualified name
start line

Recommended:

symbol_id = sha1(file_path + "|" + qualified_name + "|" + start_line)

Readable alias may also be stored.

7.5 Feature IDs

Use slug:

feature_id = "feat_" + slug(name)
7.6 Responsibility IDs

Use slug:

responsibility_id = "resp_" + slug(name)
7.7 ConfigKey IDs

Use:

config_key_id = "cfg_" + slug(name)
7.8 DataEntity IDs

Use:

data_entity_id = "de_" + slug(name)
7.9 Dependency IDs

Use:

dependency_id = "dep_" + slug(name)
7.10 Endpoint IDs

Use:

endpoint_id = "ep_" + slug(name)
8. Example Records
8.1 Example file node
{
  "file_id": "file_backend_core_queue_engine_py",
  "path": "backend/core/queue_engine.py",
  "name": "queue_engine.py",
  "extension": ".py",
  "language": "python",
  "file_type": "service",
  "line_count": 318,
  "is_entrypoint": false,
  "summary": "Handles queue task creation, lookup, and execution."
}
8.2 Example symbol node
{
  "symbol_id": "sym_queue_engine_process_task_by_id",
  "file_path": "backend/core/queue_engine.py",
  "name": "process_task_by_id",
  "qualified_name": "backend.core.queue_engine.QueueEngine.process_task_by_id",
  "symbol_type": "method",
  "start_line": 120,
  "end_line": 181,
  "signature": "def process_task_by_id(self, task_id)",
  "docstring_summary": "Executes a queued task by ID and updates its status.",
  "is_entrypoint": false
}
8.3 Example config node
{
  "config_key_id": "cfg_dev_allow_insecure_cookies",
  "name": "DEV_ALLOW_INSECURE_COOKIES",
  "source_type": "env_var",
  "description": "Allows insecure cookies in development environments."
}
8.4 Example data entity node
{
  "data_entity_id": "de_process_queue",
  "name": "process_queue",
  "entity_type": "table",
  "description": "Queue table storing tasks, states, and processing metadata."
}
8.5 Example import relationship
{
  "edge_type": "IMPORTS",
  "source_id": "file_backend_core_outline_manager_py",
  "target_id": "file_backend_core_transcript_manager_py",
  "origin": "extracted"
}
8.6 Example call relationship
{
  "edge_type": "CALLS",
  "source_id": "sym_outline_manager_generate_summary_and_toc",
  "target_id": "sym_prompt_builder_build",
  "origin": "derived"
}
8.7 Example config relationship
{
  "edge_type": "READS_CONFIG",
  "source_id": "file_app_main_py",
  "target_id": "cfg_debug",
  "origin": "extracted"
}
8.8 Example feature relationship
{
  "edge_type": "BELONGS_TO_FEATURE",
  "source_id": "file_backend_core_outline_manager_py",
  "target_id": "feat_outline_generation",
  "origin": "inferred"
}
9. V1 Included vs Excluded
Included in V1
folders
files
classes
functions
methods
module imports
external dependencies
config key usage
basic data entity references
basic entrypoints
feature and responsibility labels
file and symbol relationships
Excluded from V1
local variables
full AST trees as stored nodes
every string literal
every comment as a node
every intermediate expression
full runtime call tracing
full control-flow graphs
complete framework-specific semantic resolution
full type inference
perfect dynamic import resolution
automatic code editing
10. V1 Prioritization Rules

When in doubt, include only nodes and edges that help with:

scope selection
change safety
dependency understanding
feature mapping
AI context compression

A relationship should not be included just because it exists.
It should be included because it helps the system answer a real engineering question.

11. Implementation Notes
Recommended V1 build order
file discovery
folder hierarchy extraction
Python AST symbol extraction
import extraction
config key extraction
data entity extraction
file type classification
feature/responsibility labeling
graph load format
Kùzu projection
scope query builder
Canonical storage recommendation

Store extracted and classified records in JSON first, then project into Kùzu.

This keeps the system:

portable
debuggable
rebuildable
easy to test
12. Final Summary

V1 of AIS-RTS should establish a disciplined graph model centered on files, symbols, features, responsibilities, configs, entities, dependencies, and entrypoints. The model should stay compact, readable, and directly useful for AI-assisted code understanding.

The goal is not to capture everything in the codebase.
The goal is to capture the relationships that matter most when deciding what code is involved in a requested change.
