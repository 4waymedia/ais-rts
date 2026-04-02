Progressive Initialization and Recursive Expansion
1. Purpose

AIS-RTS should support progressive initialization so large or complex repositories can be onboarded without requiring a costly full-project scan.

Some projects are too large, too active, or too broad to scan all at once. In many real workflows, the user only cares about a single folder, one feature area, or one entry file related to a current task. Because of that, initialization should be flexible and incremental.

The goal is to let project intelligence grow in layers:

from a file
to a folder
to a subsystem
to a feature area
to a larger project map over time

This makes AIS-RTS more practical, faster to adopt, and better suited for AI-assisted workflows where scope matters more than full coverage.

2. Core Principle

Initialization should support controlled expansion, not require full extraction.

AIS-RTS should allow users to begin with the smallest useful target and expand only as needed. Each scan pass should add meaningful knowledge to the project graph while preserving the option to deepen understanding later.

This turns project onboarding into a guided intelligence-building process rather than a one-time bulk scan.

3. Why Progressive Initialization Matters

A full-project scan is useful in some cases, but it should not be the default requirement for every repository.

Reasons full scans may not be practical
the repository is too large
the repository includes many unrelated subsystems
the user only cares about one current feature or bug
the project is actively changing
the user wants faster onboarding
scanning everything would consume too much time, compute, or token budget
Benefits of progressive initialization
faster startup for large repos
lower compute cost
smaller, more targeted context
better user control
more useful AI workflows
easier incremental adoption
4. Initialization Modes

AIS-RTS should support multiple initialization modes.

4.1 Full Project Scan

Scans the full repository and creates the most complete initial intelligence map.

Best used when
the repo is manageable in size
the user wants a long-term full intelligence baseline
the project is stable enough to justify wider indexing
Characteristics
scans all included folders
extracts all supported files
builds broad graph coverage
highest upfront cost
4.2 Folder Scan

Scans one folder and optionally its children.

Best used when
the user wants to focus on one subsystem
the project is too large for full extraction
a team works in clear architectural zones
Characteristics
bounded by path
can use filesystem depth rules
useful for backend modules, frontend feature folders, workers, or CLI areas
4.3 File Scan

Scans one file as the entrypoint for initialization.

Best used when
the user knows the core file already
the task is narrow
the user wants minimal upfront extraction
Characteristics
fastest starting point
ideal for guided recursive expansion
useful for manager classes, route files, workers, and entry scripts
4.4 Request-Driven Scan

Begins from a natural-language request instead of a manually selected file or folder.

Best used when
the user starts with a task rather than a file
the relevant project area is not yet known
AIS-RTS should discover likely scope candidates automatically
Characteristics
resolves likely starting files or features from request language
can scan only likely relevant areas first
strongest alignment with AI-assisted development workflows
Example requests
“Add dark mode support”
“Fix guest login redirect”
“Update chapter enrichment to run in parallel”
“Find what controls payment retry logic”
5. Progressive Initialization Flow

A progressive initialization flow should look like this:

User selects scan mode
User provides target file, folder, or request
AIS-RTS extracts structural facts for the selected target
AIS-RTS classifies initial semantics
AIS-RTS identifies nearby relevant files, symbols, configs, and entities
AIS-RTS suggests possible next expansions
User chooses whether to expand, stop, or save the current scope

This makes initialization iterative and interactive rather than all-or-nothing.

6. Recursive Expansion

Recursive expansion allows AIS-RTS to grow project understanding outward from an initial scan target.

The system should not only analyze what was scanned. It should also identify what is most relevant to scan next.

This allows a small initial scan to become a broader intelligence map in a controlled way.

7. Two Types of Depth

AIS-RTS should distinguish between filesystem depth and relationship depth.

7.1 Filesystem Depth

Filesystem depth controls how far downward AIS-RTS scans through directories and subdirectories.

Example

If the target is:

backend/

and filesystem depth is 1, AIS-RTS scans:

backend/
immediate child folders

If filesystem depth is 2, AIS-RTS scans:

backend/
child folders
grandchildren folders

This is useful for bounded folder scans.

7.2 Relationship Depth

Relationship depth controls how far AIS-RTS expands through structural and semantic relationships.

Relationship expansion examples
direct imports
symbol calls
config usage
data entity usage
entrypoint links
feature membership
workflow adjacency
likely impact relationships
Example

If the initial file is outline_manager.py:

Depth 0

only the file and its directly defined symbols

Depth 1

direct imports
direct configs
direct entities
direct entrypoints

Depth 2

files imported by imported files
called symbols of called symbols
adjacent workflow files
shared entity neighbors

Depth 3

broader impact zone
related services and orchestration layers
additional transitive connections

This creates a meaningful expansion path without requiring broad scanning upfront.

8. Expansion Prioritization

Recursive expansion should be ranked so AIS-RTS suggests the most useful next targets first.

8.1 High-priority expansion targets
direct imports
directly called symbols
config files directly read
DB entities directly used
explicit routes, CLI files, workers, and scheduled jobs
strong feature ownership matches
8.2 Medium-priority expansion targets
sibling files in the same subsystem
shared responsibility labels
shared data entities
files frequently co-mentioned in semantic classifications
8.3 Low-priority expansion targets
generic utility modules
broad transitive dependencies
weakly related helpers
distant shared dependencies

This ranking helps prevent scope explosion.

9. Guided Expansion

AIS-RTS should support guided expansion, where the system recommends the next best files, folders, or symbols to scan after each pass.

The goal is not blind recursion. The goal is intelligent next-step guidance.

Example guided response
{
  "scanned_target": "backend/core/outline_manager.py",
  "scan_mode": "file",
  "relationship_depth": 1,
  "suggested_next_targets": [
    {
      "target_type": "file",
      "target": "backend/core/transcript_manager.py",
      "priority": "high",
      "reason": "direct import and shared transcript workflow"
    },
    {
      "target_type": "file",
      "target": "backend/core/prompt_builder.py",
      "priority": "high",
      "reason": "used during outline prompt construction"
    },
    {
      "target_type": "file",
      "target": "backend/core/extraction_manager.py",
      "priority": "medium",
      "reason": "adjacent manager in the same subsystem"
    }
  ],
  "recommended_next_relationship_depth": 2
}

This allows the user or AI workflow to decide whether to continue expanding.

10. Scan Constraints and Budgets

Progressive initialization should support stopping conditions so scans remain manageable.

Recommended scan controls
max_files
max_symbols
max_relationships
max_depth
max_time_seconds
max_token_estimate

These constraints help AIS-RTS remain practical in large systems and AI-driven workflows.

11. Suggested Initialization Config

AIS-RTS should support a structured initialization request format.

Example: folder-based progressive scan
{
  "init_mode": "progressive",
  "scan_mode": "folder",
  "target": "backend/core",
  "filesystem_depth": 1,
  "relationship_depth": 1,
  "max_files": 50,
  "suggest_next_targets": true,
  "include_semantic_classification": true
}
Example: file-based scan
{
  "init_mode": "progressive",
  "scan_mode": "file",
  "target": "backend/core/outline_manager.py",
  "relationship_depth": 2,
  "max_files": 20,
  "suggest_next_targets": true
}
Example: request-driven scan
{
  "init_mode": "progressive",
  "scan_mode": "request_driven",
  "request": "Add parallel chapter enrichment worker",
  "relationship_depth": 2,
  "max_files": 30,
  "suggest_next_targets": true
}
12. Suggested Scan Result Contract

Each progressive scan should return both extracted results and recommended next actions.

{
  "scan_summary": {
    "scan_mode": "file",
    "target": "backend/core/outline_manager.py",
    "files_scanned": 4,
    "symbols_found": 11,
    "imports_resolved": 7,
    "config_keys_found": 2,
    "data_entities_found": 3
  },
  "scope_summary": {
    "primary_responsibility": "outline_generation",
    "matched_features": [
      "outline_generation",
      "chapter_processing"
    ]
  },
  "next_suggestions": [
    {
      "target_type": "file",
      "target": "backend/core/transcript_manager.py",
      "priority": "high",
      "reason": "high dependency relevance"
    },
    {
      "target_type": "file",
      "target": "backend/core/prompt_builder.py",
      "priority": "high",
      "reason": "shared outline workflow"
    }
  ]
}
13. Role in the Larger System

Progressive initialization should become part of the broader AIS-RTS initialization model.

Extraction Layer

Scans selected files, folders, or request-driven candidates.

Semantic Layer

Classifies what the scanned targets appear to do.

Graph Layer

Stores the extracted and inferred relationships.

Guidance Layer

Recommends the next most useful scan targets.

Scope Query Layer

Uses the accumulated project intelligence to answer user requests more accurately over time.

This means initialization is no longer a one-time event. It becomes an expandable knowledge-building process.

14. Design Recommendations
Recommendation 1

Treat full-project scanning as optional, not mandatory.

Recommendation 2

Support file, folder, and request-driven starting points from the beginning.

Recommendation 3

Keep filesystem depth and relationship depth separate.

Recommendation 4

Use guided recursive expansion instead of unrestricted recursion.

Recommendation 5

Always return suggested next targets after a scan.

Recommendation 6

Allow users to stop after a useful partial scan and still generate scope responses from partial intelligence.

That last point is especially important. Partial knowledge should still be valuable.

15. Final Summary

AIS-RTS should support progressive initialization so large repositories can be onboarded incrementally instead of requiring a full scan upfront. Users should be able to begin from a file, folder, subsystem, or natural-language request, then expand project intelligence outward through guided recursive suggestions. By separating filesystem depth from relationship depth and by ranking the next most relevant scan targets, AIS-RTS can scale from narrow task-focused onboarding to broader project understanding without wasting time, compute, or context.
