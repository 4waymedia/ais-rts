AIS-RTS Implementation Spec
1. Overview

AIS-RTS is a Scope Relationship Tracking System designed to create a compact, queryable intelligence layer for software projects. Its purpose is to help AI and developers understand the exact files, symbols, dependencies, configurations, data entities, and workflows involved in a requested change before code is modified.

The system transforms raw code structure into a relationship-aware model that can answer questions such as:

What files are involved in this feature?
What functions or classes control this behavior?
What dependencies are upstream and downstream?
What config keys or database entities are involved?
What is the smallest safe edit scope?

AIS-RTS is not intended to replace the source code. It is intended to create a small, high-value structural map that improves planning, change safety, developer speed, and AI accuracy.

2. Primary Goals
Goal 1 — Build a Codebase Intelligence Layer

Create a structured system that understands a project as connected parts rather than isolated text files.

Goal 2 — Reduce AI Guesswork

Provide AI with a compact scope package instead of forcing it to inspect large portions of the repository every time.

Goal 3 — Improve Change Precision

Help identify the exact files, symbols, dependencies, and impact zones involved in a request.

Goal 4 — Support Traversal-Based Reasoning

Use graph relationships to answer structural questions more effectively than keyword or embedding search alone.

Goal 5 — Create a Reusable Project Knowledge Model

Generate a metadata layer that can later support documentation, change risk analysis, debugging, planning, and onboarding.

3. Non-Goals

The first version of AIS-RTS will not attempt to:

fully understand runtime behavior in all dynamic languages
store every variable, statement, or syntax node
replace full-text code search
replace vector search
act as the only source of truth
automatically modify code
infer perfect execution flow across all frameworks and metaprogramming patterns

The system is designed to track meaningful project structure and relationships, not every detail of code exhaust.

4. Guiding Principle

Graph the meaning, not the exhaust.

AIS-RTS should prioritize:

structure
responsibility
relevance
connection
impact

over raw volume.

This ensures the graph remains useful, compact, and practical for AI-assisted workflows.

5. High-Level Architecture

AIS-RTS will be built as five main layers:

Repository / Filesystem
    ↓
Extraction Layer
    ↓
Semantic Classification Layer
    ↓
Canonical Metadata Layer
    ↓
Graph Projection Layer (Kùzu)
    ↓
Scope Query / AI Context Layer
5.1 Extraction Layer

The extraction layer scans source files and gathers structured facts such as:

file paths
file extensions
imports
classes
functions
methods
constants
config keys
route definitions
database entity references
5.2 Semantic Classification Layer

This layer adds meaning to extracted facts, such as:

file role
symbol responsibility
feature association
workflow position
entrypoint relevance
likely impact classification
5.3 Canonical Metadata Layer

This layer stores normalized extracted and classified data in a stable format, such as JSON or relational storage. This is the durable source of truth used to rebuild graph projections.

5.4 Graph Projection Layer

This layer loads the canonical metadata into Kùzu for traversal-heavy relationship queries.

5.5 Scope Query / AI Context Layer

This layer accepts a user request and returns a compact scope package containing the most relevant files, symbols, dependencies, configs, and impact zones.

6. Core Workflow

When a user requests a feature improvement or bug fix, AIS-RTS should follow this flow:

Accept the request
Match the request to known features, responsibilities, or symbols
Traverse related files, symbols, configs, entities, and workflows
Rank the most relevant results
Produce a small structured scope response
Use that response as the context foundation for AI review or implementation
7. V1 Scope

Version 1 should stay focused and practical.

V1 Language Support
Python only
V1 Capabilities
scan repository files
classify file type
extract imports
extract classes and functions
detect basic config/environment key usage
detect basic DB table/entity references
create file-to-symbol and file-to-file relationships
associate files and symbols to feature labels
store data in canonical JSON
load graph into Kùzu
answer basic scope questions for AI
V1 Supported Query Types
What files are involved in this request?
What symbols matter most?
What files import or depend on this file?
What config keys affect this area?
What DB entities are read or written here?
What is the smallest likely change surface?
8. Entity Model
8.1 Project

Represents the top-level repository or application.

Fields

project_id
name
root_path
description
primary_language
8.2 Folder

Represents directory structure.

Fields

folder_id
path
name
parent_path
8.3 File

Represents a source file or important project file.

Fields

file_id
path
name
extension
language
file_type
size_bytes
line_count
hash
summary
is_entrypoint

Examples of file_type

class_module
helper
config
route
worker
schema
test
sql
prompt
component
model
service
cli
8.4 Symbol

Represents a defined code unit.

Fields

symbol_id
file_path
name
qualified_name
symbol_type
signature
visibility
start_line
end_line
docstring_summary
responsibility
is_entrypoint

Examples of symbol_type

class
function
method
constant
route_handler
task_runner
8.5 Feature

Represents a business or system capability.

Fields

feature_id
name
slug
description
category

Examples

chapter_enrichment
transcript_ingestion
user_auth
credits
supplement_generation
8.6 Responsibility

Represents the purpose a file or symbol serves.

Fields

responsibility_id
name
description

Examples

authentication
queue_processing
db_access
transcript_loading
outline_generation
8.7 ConfigKey

Represents a configuration key or environment variable.

Fields

config_key_id
name
source_type
default_value
description

Examples of source_type

env_var
json_config
yaml_config
python_constant
8.8 DataEntity

Represents a database table, model, queue object, or structured entity.

Fields

data_entity_id
name
entity_type
description

Examples of entity_type

table
view
queue_record
json_object
cache_key
8.9 ExternalDependency

Represents a package, library, framework, or service dependency.

Fields

dependency_id
name
dependency_type
version
description

Examples of dependency_type

python_package
api_service
db_driver
framework
8.10 Endpoint

Represents an API route, CLI entry, worker start point, or scheduled task entrypoint.

Fields

endpoint_id
name
endpoint_type
identifier
description

Examples of endpoint_type

api_route
cli_command
worker_job
scheduled_task
8.11 TaskFlow

Represents a multi-step system workflow.

Fields

taskflow_id
name
description
category
9. Relationship Model
9.1 Structural Relationships
(Project)-[:HAS_FOLDER]->(Folder)
(Folder)-[:HAS_FILE]->(File)
(File)-[:DEFINES]->(Symbol)
9.2 Dependency Relationships
(File)-[:IMPORTS]->(File)
(File)-[:DEPENDS_ON]->(File)
(Symbol)-[:CALLS]->(Symbol)
(Symbol)-[:USES_DEPENDENCY]->(ExternalDependency)
9.3 Responsibility Relationships
(File)-[:IMPLEMENTS]->(Responsibility)
(Symbol)-[:IMPLEMENTS]->(Responsibility)
9.4 Feature Relationships
(File)-[:BELONGS_TO_FEATURE]->(Feature)
(Symbol)-[:BELONGS_TO_FEATURE]->(Feature)
(Feature)-[:USES_FILE]->(File)
9.5 Config Relationships
(File)-[:READS_CONFIG]->(ConfigKey)
(Symbol)-[:READS_CONFIG]->(ConfigKey)
9.6 Data Relationships
(Symbol)-[:READS_ENTITY]->(DataEntity)
(Symbol)-[:WRITES_ENTITY]->(DataEntity)
(File)-[:USES_ENTITY]->(DataEntity)
9.7 Entrypoint Relationships
(Endpoint)-[:ENTRYPOINT_TO]->(Symbol)
(Endpoint)-[:USES_FILE]->(File)
9.8 Workflow Relationships
(TaskFlow)-[:STARTS_WITH]->(Symbol)
(TaskFlow)-[:USES_FILE]->(File)
(TaskFlow)-[:USES_ENTITY]->(DataEntity)
(TaskFlow)-[:RELATED_TO_FEATURE]->(Feature)
9.9 Impact Relationships
(File)-[:LIKELY_IMPACTS]->(File)
(Symbol)-[:LIKELY_IMPACTS]->(Symbol)

These can be derived from imports, calls, shared entities, and workflow adjacency.

10. Canonical Metadata Contracts
10.1 File Metadata Contract
{
  "path": "backend/core/outline_manager.py",
  "name": "outline_manager.py",
  "extension": ".py",
  "language": "python",
  "file_type": "service",
  "line_count": 420,
  "imports": [
    "backend.core.transcript_manager",
    "backend.core.prompt_builder"
  ],
  "config_keys_used": [
    "DEBUG",
    "MEDIA_ROOT"
  ],
  "data_entities_used": [
    "video_outlines",
    "video_data"
  ],
  "symbols": [
    "OutlineManager",
    "generate_summary_and_toc"
  ],
  "responsibilities": [
    "outline_generation"
  ],
  "features": [
    "chapter_enrichment",
    "outline_generation"
  ],
  "is_entrypoint": false
}
10.2 Symbol Metadata Contract
{
  "name": "OutlineManager",
  "qualified_name": "backend.core.outline_manager.OutlineManager",
  "symbol_type": "class",
  "signature": "class OutlineManager",
  "start_line": 10,
  "end_line": 420,
  "docstring_summary": "Builds and saves structured outlines from transcripts.",
  "responsibility": "outline_generation",
  "calls": [
    "PromptBuilder.build",
    "TranscriptManager.get_chunk_in_range"
  ],
  "reads_config": [
    "DEBUG"
  ],
  "reads_entities": [
    "video_data"
  ],
  "writes_entities": [
    "video_outlines"
  ],
  "belongs_to_features": [
    "outline_generation"
  ],
  "is_entrypoint": false
}
10.3 Scope Response Contract

This is the main output AI should receive for a request.

{
  "request": "Add an enrichment worker that processes 10 chapters simultaneously",
  "matched_features": [
    "chapter_enrichment",
    "worker_processing"
  ],
  "entrypoints": [
    {
      "type": "cli_command",
      "identifier": "python3 cli/test_full_chapter_enrichment.py ID --all"
    }
  ],
  "core_files": [
    {
      "path": "automation/enrichment_worker.py",
      "role": "worker",
      "reason": "new orchestration entrypoint"
    },
    {
      "path": "backend/core/chapter_enricher.py",
      "role": "service",
      "reason": "current enrichment implementation"
    },
    {
      "path": "backend/core/queue_engine.py",
      "role": "queue",
      "reason": "task execution and status"
    }
  ],
  "core_symbols": [
    {
      "name": "ChapterEnricher",
      "symbol_type": "class",
      "reason": "current chapter enrichment logic"
    }
  ],
  "config_keys": [
    "MAX_CONCURRENT_CHAPTERS",
    "COST_LIMIT_USD"
  ],
  "data_entities": [
    "process_queue",
    "video_chapters",
    "token_usage_log"
  ],
  "likely_impacts": [
    "worker scheduling",
    "chapter state updates",
    "token accounting",
    "retry behavior"
  ],
  "review_order": [
    "current CLI enrichment flow",
    "chapter enrichment service",
    "queue handling and status persistence"
  ]
}
11. Extraction Strategy for V1

V1 should support Python only.

Extraction methods
Python ast for classes, functions, imports, constants
regex fallback for:
os.getenv(...)
known config access patterns
SQL table names in string queries
route decorators
optional libcst later for richer syntax analysis
Extracted facts in V1
file path and type
imports
classes
functions
methods
decorators
config keys
likely DB entities
docstrings
line ranges
12. Semantic Classification Strategy

The semantic layer should classify files and symbols using a mix of rules and AI assistance.

Rule-based examples
file path includes /routes/ → route
file path includes /workers/ → worker
file name includes config → config
file path includes /cli/ → cli
class name ends with Manager → likely service or orchestrator
AI-assisted classifications
summary of what the file does
responsibility label
likely feature ownership
likely impact category
review priority

The system should store both:

extracted facts
semantic inferences

These must remain separate so inferences can be regenerated without altering source facts.

13. Graph Projection Strategy

Kùzu will be used as a graph projection layer, not as the only source of truth.

Recommended pattern
canonical metadata stored as JSON
graph rebuilt from canonical metadata when needed
Kùzu used for relationship traversal and scope generation
Why

This keeps the system portable, debuggable, and resilient if graph-layer tooling changes later.

14. Scope Query Strategy

When a new request arrives, the system should:

parse the request
detect likely feature and responsibility keywords
locate matching files and symbols
traverse dependency and impact relationships
rank by relevance
compress results into a small scope package
Ranking signals
direct feature match
entrypoint proximity
symbol centrality
config coupling
shared data entity usage
workflow relevance
file role importance
15. V1 Directory Proposal
ais-rts/
├── README.md
├── docs/
│   ├── implementation_spec.md
│   ├── v1_entities_and_relationships.md
│   └── scope_response_examples.md
├── src/
│   ├── extractors/
│   │   ├── python_extractor.py
│   │   ├── config_extractor.py
│   │   └── sql_entity_extractor.py
│   ├── classifiers/
│   │   ├── file_classifier.py
│   │   └── feature_classifier.py
│   ├── models/
│   │   ├── file_model.py
│   │   ├── symbol_model.py
│   │   └── relationship_model.py
│   ├── graph/
│   │   ├── kuzu_loader.py
│   │   └── query_engine.py
│   ├── scope/
│   │   ├── scope_builder.py
│   │   └── ranking_engine.py
│   └── main.py
└── data/
    ├── extracted/
    ├── canonical/
    └── graph/
16. Success Criteria for V1

AIS-RTS V1 is successful if it can:

scan a Python repository and extract core file/symbol metadata
classify files into meaningful file types
identify imports and symbol definitions
identify basic config keys and data entities
load those relationships into Kùzu
answer basic scope questions with compact JSON output
reduce the number of files AI needs to inspect for a request
17. Future Phases
V2
better symbol-to-symbol call graph
deeper route and worker flow detection
better DB model extraction
test coverage relationships
change risk scoring
V3
multi-language support
Git history awareness
ownership and author patterns
runtime telemetry integration
richer workflow and execution modeling
V4
AI planning mode
automated edit-scope recommendations
documentation generation from graph structure
architecture maps and visualizations
18. Final Summary

AIS-RTS is a codebase intelligence system that builds a compact structural relationship model of files, symbols, dependencies, configurations, data entities, and workflows. Its goal is to give AI and developers a precise scope map before code changes are made, improving context accuracy, change safety, and implementation speed.

The first version should remain focused: Python extraction, canonical metadata, Kùzu graph projection, and compact scope responses for AI-assisted development.
