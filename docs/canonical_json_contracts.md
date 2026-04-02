Canonical JSON Contracts
1. Purpose

This document defines the canonical JSON contracts used by AIS-RTS to move data cleanly between each system layer.

These contracts create a stable handoff between:

repository scanner
extractor
classifier
graph loader
scope builder
AI query layer

The purpose of these contracts is to ensure that extracted facts, inferred meaning, derived relationships, and AI-ready scope outputs all follow a predictable structure.

This gives AIS-RTS:

portability
rebuildability
consistency
easier debugging
easier testing
easier graph regeneration
2. Contract Design Principles
2.1 Facts and meaning must remain separate

Raw extracted facts should not be mixed with semantic inference.

Examples:

imports, line ranges, and symbol names are facts
file responsibility, feature ownership, and change impact are inferences
2.2 Canonical data should be graph-neutral

The canonical JSON should not depend on Kùzu-specific structures.
It should be usable for:

graph loading
testing
reporting
alternate storage layers
2.3 IDs must be stable

All records should use stable identifiers so the graph can be rebuilt reliably across runs.

2.4 Output should be compact but complete

Store enough detail to support traversal and AI context generation, but do not bloat the structure with unnecessary syntax details.

3. Canonical Data Layers

AIS-RTS should treat canonical JSON as four distinct layers:

Layer 1 — Project Manifest

Top-level project metadata and scan boundaries.

Layer 2 — Extracted Facts

Raw file, symbol, import, config, dependency, entity, and endpoint records.

Layer 3 — Semantic Inferences

Responsibilities, feature mappings, summaries, and semantic classifications.

Layer 4 — Derived Relationships

Resolved dependencies, impact links, ranking hints, and AI scope outputs.

4. File Layout Recommendation
data/
├── extracted/
│   ├── project_manifest.json
│   ├── folders.json
│   ├── files.json
│   ├── symbols.json
│   ├── imports.json
│   ├── config_keys.json
│   ├── data_entities.json
│   ├── external_dependencies.json
│   └── endpoints.json
├── semantic/
│   ├── file_semantics.json
│   ├── symbol_semantics.json
│   ├── features.json
│   └── responsibilities.json
├── derived/
│   ├── resolved_dependencies.json
│   ├── impact_relationships.json
│   └── scope_candidates.json
└── scope/
    └── latest_scope_response.json

This structure keeps each layer clean and easy to inspect.

5. Project Manifest Contract

This defines the project being scanned.

{
  "project_id": "proj_ais_rts",
  "name": "ais-rts",
  "root_path": "/Users/paul/code/ais-rts",
  "primary_language": "python",
  "description": "AI Scope Relationship Tracking System",
  "scan_timestamp": "2026-04-01T18:00:00Z",
  "scan_version": "v1",
  "included_paths": [
    "src",
    "backend",
    "cli",
    "automation"
  ],
  "excluded_paths": [
    "__pycache__",
    ".venv",
    "node_modules",
    "dist",
    "build"
  ]
}
Required fields
project_id
name
root_path
primary_language
scan_timestamp
scan_version
Optional fields
description
included_paths
excluded_paths
6. Folder Record Contract
{
  "folder_id": "fld_src_extractors",
  "project_id": "proj_ais_rts",
  "path": "src/extractors",
  "name": "extractors",
  "parent_path": "src"
}
Required fields
folder_id
project_id
path
name
Optional fields
parent_path
7. File Record Contract

This is the main canonical record for each file.

{
  "file_id": "file_backend_core_outline_manager_py",
  "project_id": "proj_ais_rts",
  "folder_path": "backend/core",
  "path": "backend/core/outline_manager.py",
  "name": "outline_manager.py",
  "extension": ".py",
  "language": "python",
  "file_type": "service",
  "line_count": 420,
  "size_bytes": 18422,
  "hash": "sha1:abc123xyz",
  "is_entrypoint": false,
  "raw_imports": [
    "backend.core.transcript_manager",
    "backend.core.prompt_builder",
    "os"
  ],
  "docstring_summary": "Builds and saves structured outlines from transcripts."
}
Required fields
file_id
project_id
path
name
extension
language
file_type
Optional fields
folder_path
line_count
size_bytes
hash
is_entrypoint
raw_imports
docstring_summary
8. Symbol Record Contract

This represents classes, functions, methods, and constants.

{
  "symbol_id": "sym_outline_manager",
  "project_id": "proj_ais_rts",
  "file_id": "file_backend_core_outline_manager_py",
  "file_path": "backend/core/outline_manager.py",
  "name": "OutlineManager",
  "qualified_name": "backend.core.outline_manager.OutlineManager",
  "symbol_type": "class",
  "signature": "class OutlineManager",
  "parent_symbol": null,
  "visibility": "public",
  "start_line": 10,
  "end_line": 420,
  "decorators": [],
  "docstring_summary": "Builds and saves structured outlines from transcripts.",
  "is_entrypoint": false
}
Required fields
symbol_id
project_id
file_id
file_path
name
qualified_name
symbol_type
start_line
end_line
Optional fields
signature
parent_symbol
visibility
decorators
docstring_summary
is_entrypoint
9. Import Record Contract

Imports should be stored as separate canonical records so they can later be resolved to internal files or external dependencies.

{
  "import_id": "imp_outline_manager_transcript_manager",
  "project_id": "proj_ais_rts",
  "source_file_id": "file_backend_core_outline_manager_py",
  "source_path": "backend/core/outline_manager.py",
  "import_text": "backend.core.transcript_manager",
  "import_type": "module",
  "resolved_kind": "internal_file",
  "resolved_target_id": "file_backend_core_transcript_manager_py"
}
Required fields
import_id
project_id
source_file_id
source_path
import_text
Optional fields
import_type
resolved_kind
resolved_target_id
resolved_kind examples
internal_file
external_dependency
unresolved
10. Config Key Contract
{
  "config_key_id": "cfg_debug",
  "project_id": "proj_ais_rts",
  "name": "DEBUG",
  "source_type": "env_var",
  "default_value": "false",
  "description": "Controls debug mode."
}
Required fields
config_key_id
project_id
name
source_type
Optional fields
default_value
description
11. Config Usage Contract

This links files or symbols to config usage as extracted facts.

{
  "config_usage_id": "cfguse_app_main_debug",
  "project_id": "proj_ais_rts",
  "config_key_id": "cfg_debug",
  "source_kind": "file",
  "source_id": "file_app_main_py",
  "source_path": "app/main.py",
  "usage_type": "read",
  "line_number": 18
}
Required fields
config_usage_id
project_id
config_key_id
source_kind
source_id
usage_type
Optional fields
source_path
line_number
source_kind values
file
symbol
usage_type values
read
write
default_definition
12. Data Entity Contract
{
  "data_entity_id": "de_video_outlines",
  "project_id": "proj_ais_rts",
  "name": "video_outlines",
  "entity_type": "table",
  "description": "Stores structured outline results for videos."
}
Required fields
data_entity_id
project_id
name
entity_type
Optional fields
description
13. Data Entity Usage Contract
{
  "entity_usage_id": "entuse_outline_manager_video_outlines_write",
  "project_id": "proj_ais_rts",
  "data_entity_id": "de_video_outlines",
  "source_kind": "symbol",
  "source_id": "sym_outline_manager_save_outline",
  "source_path": "backend/core/outline_manager.py",
  "usage_type": "write",
  "line_number": 212
}
Required fields
entity_usage_id
project_id
data_entity_id
source_kind
source_id
usage_type
Optional fields
source_path
line_number
usage_type values
read
write
read_write
14. External Dependency Contract
{
  "dependency_id": "dep_fastapi",
  "project_id": "proj_ais_rts",
  "name": "fastapi",
  "dependency_type": "framework",
  "version": null,
  "description": "FastAPI web framework"
}
Required fields
dependency_id
project_id
name
dependency_type
Optional fields
version
description
15. Dependency Usage Contract
{
  "dependency_usage_id": "depuse_app_main_fastapi",
  "project_id": "proj_ais_rts",
  "dependency_id": "dep_fastapi",
  "source_kind": "file",
  "source_id": "file_app_main_py",
  "source_path": "app/main.py",
  "line_number": 3
}
Required fields
dependency_usage_id
project_id
dependency_id
source_kind
source_id
Optional fields
source_path
line_number
16. Endpoint Contract
{
  "endpoint_id": "ep_cli_full_chapter_enrichment",
  "project_id": "proj_ais_rts",
  "name": "Full Chapter Enrichment CLI",
  "endpoint_type": "cli_command",
  "identifier": "python3 cli/test_full_chapter_enrichment.py ID --all",
  "description": "Runs chapter enrichment for a single video."
}
Required fields
endpoint_id
project_id
name
endpoint_type
identifier
Optional fields
description
17. Endpoint Binding Contract

This binds an endpoint to the file or symbol it enters.

{
  "endpoint_binding_id": "epbind_full_chapter_enrichment",
  "project_id": "proj_ais_rts",
  "endpoint_id": "ep_cli_full_chapter_enrichment",
  "target_kind": "symbol",
  "target_id": "sym_run_full_chapter_enrichment",
  "target_path": "cli/test_full_chapter_enrichment.py",
  "binding_type": "entrypoint_to"
}
Required fields
endpoint_binding_id
project_id
endpoint_id
target_kind
target_id
binding_type
Optional fields
target_path
target_kind values
file
symbol
binding_type values
entrypoint_to
uses_file
18. Responsibility Contract

This is semantic, not raw extracted data.

{
  "responsibility_id": "resp_outline_generation",
  "project_id": "proj_ais_rts",
  "name": "outline_generation",
  "description": "Builds and saves structured outlines from transcript data."
}
Required fields
responsibility_id
project_id
name
Optional fields
description
19. Feature Contract
{
  "feature_id": "feat_outline_generation",
  "project_id": "proj_ais_rts",
  "name": "Outline Generation",
  "slug": "outline_generation",
  "category": "content_pipeline",
  "description": "Creates structured outlines from transcript data."
}
Required fields
feature_id
project_id
name
slug
Optional fields
category
description
20. File Semantic Contract

This contains semantic inferences for files.

{
  "file_semantic_id": "filesem_outline_manager",
  "project_id": "proj_ais_rts",
  "file_id": "file_backend_core_outline_manager_py",
  "responsibility_ids": [
    "resp_outline_generation"
  ],
  "feature_ids": [
    "feat_outline_generation"
  ],
  "summary": "Primary service that generates and stores outlines.",
  "importance_score": 0.92,
  "is_core_file": true,
  "classification_origin": "ai"
}
Required fields
file_semantic_id
project_id
file_id
Optional fields
responsibility_ids
feature_ids
summary
importance_score
is_core_file
classification_origin
classification_origin values
rule
ai
hybrid
21. Symbol Semantic Contract
{
  "symbol_semantic_id": "symsem_outline_manager_class",
  "project_id": "proj_ais_rts",
  "symbol_id": "sym_outline_manager",
  "responsibility_ids": [
    "resp_outline_generation"
  ],
  "feature_ids": [
    "feat_outline_generation"
  ],
  "summary": "Central class responsible for outline generation orchestration.",
  "importance_score": 0.95,
  "classification_origin": "ai"
}
Required fields
symbol_semantic_id
project_id
symbol_id
Optional fields
responsibility_ids
feature_ids
summary
importance_score
classification_origin
22. Derived Relationship Contract

This contract stores graph-neutral derived relationships before projection into Kùzu.

{
  "relationship_id": "rel_outline_manager_depends_on_transcript_manager",
  "project_id": "proj_ais_rts",
  "relationship_type": "DEPENDS_ON",
  "source_kind": "file",
  "source_id": "file_backend_core_outline_manager_py",
  "target_kind": "file",
  "target_id": "file_backend_core_transcript_manager_py",
  "strength": 0.88,
  "origin": "derived",
  "reason": "Resolved internal import and shared transcript workflow usage."
}
Required fields
relationship_id
project_id
relationship_type
source_kind
source_id
target_kind
target_id
origin
Optional fields
strength
reason
origin values
extracted
inferred
derived
23. Scope Candidate Contract

This is the intermediate ranking layer used before generating the final AI scope response.

{
  "scope_candidate_id": "sc_outline_manager_request_001",
  "project_id": "proj_ais_rts",
  "request_id": "req_001",
  "candidate_kind": "file",
  "candidate_id": "file_backend_core_outline_manager_py",
  "candidate_path": "backend/core/outline_manager.py",
  "relevance_score": 0.94,
  "match_reasons": [
    "feature_match:outline_generation",
    "entrypoint_proximity",
    "shared_data_entity:video_outlines"
  ]
}
Required fields
scope_candidate_id
project_id
request_id
candidate_kind
candidate_id
relevance_score
Optional fields
candidate_path
match_reasons
24. Final Scope Response Contract

This is the compact AI-ready context payload generated from the graph and ranking layer.

{
  "request_id": "req_001",
  "project_id": "proj_ais_rts",
  "request_text": "Add an enrichment worker that processes 10 chapters simultaneously",
  "matched_features": [
    "chapter_enrichment",
    "worker_processing"
  ],
  "entrypoints": [
    {
      "endpoint_id": "ep_cli_full_chapter_enrichment",
      "type": "cli_command",
      "identifier": "python3 cli/test_full_chapter_enrichment.py ID --all"
    }
  ],
  "core_files": [
    {
      "file_id": "file_automation_enrichment_worker_py",
      "path": "automation/enrichment_worker.py",
      "role": "worker",
      "reason": "new orchestration entrypoint"
    },
    {
      "file_id": "file_backend_core_chapter_enricher_py",
      "path": "backend/core/chapter_enricher.py",
      "role": "service",
      "reason": "current enrichment implementation"
    },
    {
      "file_id": "file_backend_core_queue_engine_py",
      "path": "backend/core/queue_engine.py",
      "role": "queue",
      "reason": "task execution and status handling"
    }
  ],
  "core_symbols": [
    {
      "symbol_id": "sym_chapter_enricher",
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
  "external_dependencies": [],
  "likely_impacts": [
    "worker scheduling",
    "chapter state updates",
    "retry behavior",
    "token accounting"
  ],
  "review_order": [
    "current CLI enrichment flow",
    "chapter enrichment service",
    "queue handling and persistence"
  ],
  "notes": [
    "Review current queue status transitions before adding worker concurrency.",
    "Confirm whether token accounting is per chapter or per worker batch."
  ]
}
Required fields
request_id
project_id
request_text
Optional fields
matched_features
entrypoints
core_files
core_symbols
config_keys
data_entities
external_dependencies
likely_impacts
review_order
notes
25. Facts vs Semantics vs Derived Rules

To keep the system disciplined, every contract should belong clearly to one layer.

Extracted facts
project_manifest.json
folders.json
files.json
symbols.json
imports.json
config_keys.json
config_usages.json
data_entities.json
data_entity_usages.json
external_dependencies.json
dependency_usages.json
endpoints.json
endpoint_bindings.json
Semantic layer
features.json
responsibilities.json
file_semantics.json
symbol_semantics.json
Derived layer
resolved_dependencies.json
impact_relationships.json
scope_candidates.json
Final AI layer
latest_scope_response.json
26. Validation Rules

Every canonical JSON file should validate these principles:

Required field validation

Each required field must exist and be non-null unless explicitly allowed.

Stable ID validation

All IDs must follow deterministic generation rules.

Enum validation

Known fields such as:

file_type
symbol_type
source_kind
usage_type
relationship_type
endpoint_type

should validate against allowed values.

Cross-reference validation

Referenced IDs should exist in their corresponding canonical files.

Examples:

file_id in symbols.json must exist in files.json
config_key_id in config_usages.json must exist in config_keys.json
feature_id in file_semantics.json must exist in features.json
27. Recommended Build Order for Contracts

Implement these in order:

project_manifest.json
folders.json
files.json
symbols.json
imports.json
config_keys.json
config_usages.json
data_entities.json
data_entity_usages.json
external_dependencies.json
dependency_usages.json
endpoints.json
endpoint_bindings.json
responsibilities.json
features.json
file_semantics.json
symbol_semantics.json
resolved_dependencies.json
impact_relationships.json
scope_candidates.json
latest_scope_response.json
28. Final Summary

The canonical JSON contracts define the stable system language of AIS-RTS. They separate extracted facts, semantic understanding, and derived relationships so the system remains portable, inspectable, and rebuildable.

These contracts should become the formal interface between:

extraction
classification
graph projection
scope generation
