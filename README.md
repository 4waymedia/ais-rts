AIS-RTS

The top goal of AIS-RTS is to eliminate AI hallucination in software projects. No more guessing function names, assuming classes exist, inventing parameters, or relying on imagined architecture. AIS-RTS gives AI a verified structural understanding of the real codebase so planning and code generation happen from facts, not assumptions.

AIS-RTS is a Scope Relationship Tracking System that helps AI and developers understand exactly what parts of a codebase are involved in a requested change before code is modified. It transforms files, symbols, dependencies, configs, data entities, and workflows into a compact intelligence layer so AI can work from precise scope instead of blindly consuming large parts of a repository.

AIS-RTS is designed around three core ideas: extract reliable structural facts from real codebases, classify meaning such as responsibilities and feature ownership, and answer natural-language scope queries with tight, actionable context. It supports progressive initialization for large projects, allowing users to scan one file, one folder, or one request area at a time, then expand outward through guided recursive suggestions. The long-term goal is to make AI-assisted coding safer, faster, and far more context-aware.

Anti-Hallucination Principle

AIS-RTS must reduce or eliminate project-level hallucination by ensuring that AI works from verified code facts whenever verification is possible. The system should extract and validate real files, symbols, parameters, dependencies, configs, and interfaces before they are used in planning or code generation. Inferred and suggested information must remain clearly labeled and must never be presented as verified project truth.

# ais-rts
AI Scope RTS

Scope Relationship Tracking System
1. Purpose

The purpose of this system is to create a structured intelligence layer for a codebase that helps AI and developers quickly understand how a project is organized, how its parts connect, and what is involved when a change is requested.

Instead of forcing AI to read large portions of the repository every time a feature, bug fix, or enhancement is requested, this system will provide a small, precise, relationship-driven context model that identifies the files, classes, functions, dependencies, configs, and operations relevant to the task.

This system is intended to reduce wasted context, improve code accuracy, speed up implementation planning, and give AI a much stronger understanding of scope before code changes begin.

2. Core Problem We Are Solving

Modern projects grow into large collections of files, classes, helpers, configs, workers, routes, and services. Even when the code is well organized, AI often lacks a reliable way to understand:

which files matter for a requested change
how files relate to each other
which functions or classes are responsible for specific behavior
what upstream and downstream dependencies exist
what configs, database tables, routes, or workflows are affected
what the smallest safe edit scope should be

Without this structure, AI tends to:

inspect too much code
miss critical dependencies
misunderstand responsibility boundaries
propose incomplete fixes
create unnecessary or risky changes

We need a system that converts the codebase into a compact relationship map so AI can reason about changes with far greater precision.

3. Vision

We are building a codebase context intelligence system that maps project structure into meaningful relationships.

The vision is to make a repository understandable not just as folders and source files, but as a living system of:

files
symbols
dependencies
responsibilities
features
workflows
data entities
configuration influence
change impact paths

This will allow AI to query the project like an intelligent system rather than treating it like a pile of text.

4. Desired Result

The desired result is a system that can take a request such as:

“Add a feature”
“Fix this bug”
“Improve this workflow”
“Refactor this module”
“Find what controls this process”

and produce a small, high-value scope response showing:

the most relevant files
the most relevant classes and functions
related dependencies
affected configs and data models
workflow connections
likely impact zones
recommended order of review

In short, the system should tell AI:

what matters, why it matters, and what is connected to it.

5. What We Are Creating

We are creating a scope relationship tracking system that scans a project and builds a structured model of code relationships.

This model will identify and connect:

folders and files
file types
imports and dependencies
classes, functions, methods, constants, and definitions
responsibilities of files and symbols
related features and workflows
config keys and environment variables
database entities and tables
entrypoints such as routes, CLI scripts, and workers
change impact relationships

This data will then be stored in a compact, queryable structure, with Kùzu serving as the graph traversal layer for relationship-heavy queries.

6. High-Level Plan
Phase 1 — Define the System

Establish the project goals, scope, terminology, and success criteria.

This phase defines:

purpose
architecture direction
what entities we track
what relationships matter
what outputs AI needs
Phase 2 — Build the Extraction Layer

Create a scanner that analyzes the repository and extracts structured facts from the codebase.

Examples:

files and folders
file type
imports
classes
functions
config usage
route definitions
DB table references
Phase 3 — Build the Semantic Layer

Add classification and meaning on top of raw extracted facts.

Examples:

file responsibility
feature ownership
workflow role
likely impact relevance
operational category
Phase 4 — Load Into Graph Structure

Store the extracted and classified metadata into a graph model that supports relationship traversal.

Kùzu will be used here as the scope relationship engine.

Phase 5 — Create Scope Output for AI

Build a system that takes a user request and returns a compact scope package containing only the most relevant project context.

Phase 6 — Integrate With AI Coding Workflow

Use the scope package as the first step before code generation, review, bug fixing, or feature planning.

7. Main Functional Goal

The main functional goal is:

When a user asks for a change, the system should identify the smallest accurate set of code context needed for AI to understand and work on that request safely and effectively.

That means the system should answer questions like:

What files are directly involved?
What symbols define the behavior?
What other files are connected?
What configs or data models influence this?
What else could break if this changes?
What should be reviewed before editing?
8. Benefits
Better AI Accuracy

AI receives relationship-based context instead of guessing from limited text windows.

Smaller Context Windows

Instead of feeding many files into the model, we provide a compact structure showing the exact scope.

Faster Development

Developers and AI can locate the relevant code paths much faster.

Safer Changes

The system improves awareness of dependencies, related components, and likely impact zones.

Better Refactoring

It becomes easier to identify tightly coupled areas, dead-end helpers, and hidden dependencies.

Better Onboarding

New developers or AI agents can understand the project more quickly.

Better Planning

Before editing anything, the system can identify what the request actually touches.

Reusable Intelligence Layer

The graph and metadata can be reused for:

feature planning
code review
debugging
architecture analysis
documentation generation
dependency audits
AI copilots
change risk summaries
9. Why This Matters for AI

AI performs best when it knows:

the relevant scope
the structure of responsibility
what is upstream and downstream
which files are central versus peripheral

Raw code search is not enough.

Vector search may find related text, but it does not reliably explain:

dependency chains
ownership boundaries
execution paths
workflow relationships
change impact

This system gives AI a structured reasoning map, which is much closer to how a strong engineer approaches a codebase.

10. Guiding Principle

A key principle for this system should be:

Graph the meaning, not the exhaust.

We do not want to store every tiny code detail.
We want to store the relationships that help AI and developers reason about change.

That means we prioritize:

structure
responsibility
connection
impact
relevance

over raw volume.

11. Success Criteria

The system is successful if it can reliably do the following:

Identify the files most relevant to a requested feature or fix
Identify the classes/functions that control the behavior
Show dependencies and related impact zones
Surface important configs, tables, routes, or workflows involved
Produce a compact context package for AI
Reduce unnecessary file loading and guesswork
Improve implementation confidence and change safety
12. Simple Project Summary

This project creates a structured relationship model of a codebase so AI can understand exactly what files, functions, dependencies, and workflows are involved in a request before making changes. The result is a compact, intelligent context layer that improves accuracy, reduces wasted context, speeds up implementation, and makes project-scale AI coding far more reliable.

13. Strong Internal Positioning Statement

You can use this as a clean project description:

The Scope Relationship Tracking System is a codebase intelligence layer that transforms files, symbols, dependencies, and workflows into a compact relationship graph, allowing AI to understand change scope with precision before generating or modifying code.

14. Short Founder-Style Version

We are building a context intelligence system for software projects. Instead of making AI read large parts of a repository blindly, this system identifies the exact files, classes, functions, dependencies, and operational relationships involved in a request, giving AI a precise scope map before any code is written.

