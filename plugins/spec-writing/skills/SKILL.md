---
name: spec-writing
description: 'Guidance for planning and writing changes to software specification documents, striving for precision and the progressive development of concepts. Enforces cross-document consistency, terminology discipline, requirements compliance, and robustness analysis using a SPECFILES.md manifest. Use this skill whenever any file in the manifest will be planned, written, or modified. Common triggers: entering plan mode for spec changes, updating the design or specification, adding or modifying features, data structures, behaviors, or workflows, defining or renaming terms, or resolving spec issues. Always use this skill for spec work, even for seemingly small changes, because even small changes can have implications across multiple documents that need to be traced. Use `/spec-writing init` to create a new SPECFILES.md manifest and starter spec files.'
---

# Spec Writing

This skill guides you through planning and writing changes to a set of specification documents. The specification is a precision instrument — it drives implementation, and inconsistencies or gaps become bugs.

## The Spec Manifest

Every project using this skill must have a **SPECFILES.md** file in the working directory. This manifest defines the project's specification structure and contains all project-specific context the skill needs. If SPECFILES.md is not present, tell the user "SPECFILES.md not present in this directory. Run `/spec-writing init` to create one." and stop — do not run any other part of this skill, do not attempt spec work, and do not proceed further. Do not search for SPECFILES.md elsewhere.

SPECFILES.md contains the following sections:

- **Documents** — The spec files in dependency/reading order. Each entry is a bullet with a backtick-quoted file path, a colon, and a description of the file's role. The ordering matters: earlier files should be readable without later ones, and later files may reference earlier ones.
- **Universal Checks** (optional) — Domain-specific robustness questions to ask when adding any new behavior. These supplement the skill's built-in robustness checks and should target failure patterns unique to the project's domain.
- **Conditional Checks** (optional) — Project-specific checks triggered by particular kinds of changes. Each bullet describes a situation and what to verify. Use this for anything the skill doesn't handle automatically — cross-document ripple patterns, domain-specific consistency rules, or other concerns.

### Terminology Management

Specifications handle terminology in one of two ways, determined by the SPECFILES.md manifest:

- **Dedicated glossary:** If the Documents section lists a file whose description indicates it contains a terminology or glossary section, that section is the central registry for all defined terms. New terms are added there before being used elsewhere.
- **Progressive introduction:** If no such file is listed, terms are introduced in context where they first become relevant. The document ordering in SPECFILES.md ensures readers encounter definitions before uses. Each term should still be defined clearly on first use — formatted according to the project's conventions — so that later references are unambiguous.

**What needs a definition.** A term needs a formal definition when its meaning in the project cannot be reliably inferred from general technical knowledge alone. This includes:

- **Project-specific coinages** — words or phrases invented for the project that have no prior meaning
- **Common words with a project-specific meaning** — everyday or general technical words that the project narrows or makes more precise than their usual sense
- **Abbreviations and shorthands** — shortened forms that readers may not recognize without a definition
- **Compound terms naming specific concepts** — multi-word names for project-specific mechanisms, structures, or roles

Standard technical terms used with their standard, commonly understood meanings do not need definitions. The test is whether a technically competent reader encountering the term for the first time in this project would understand its intended meaning without a definition.

When this skill refers to "where terms are defined," it means the glossary section if one exists, or the point of first introduction if not.

### Requirements Awareness

If the Documents section lists a file whose description indicates it contains requirements or constraints — for example, descriptions mentioning "requirements," "constraints," or similar — the requirements and constraints within that file serve as a source of truth that all other spec content must satisfy. When such a file exists:

- Read it during the planning phase before proposing changes.
- Verify that every planned change is consistent with every requirement and constraint.
- If a proposed change would violate, weaken, or be inconsistent with any requirement, do not proceed silently. Flag the specific inconsistency and ask the user how to handle it — the user may choose to modify the change, update the requirement, or accept the inconsistency with justification.

Requirements files themselves may also be modified, but changes to them should trigger the same verification in reverse: check whether existing spec content still satisfies the updated requirements.

### Scenarios Tracking

If the Documents section lists a file whose description indicates it collects scenarios, edge cases, or test cases, that file is the **scenarios section**. When new behaviors are added or robustness questions surface relevant situations, add scenarios to this file using its existing format. If no such file exists, scenarios are not tracked separately.

### Example

```markdown
Document order defines reading order, where earlier documents introduce terms and concepts used by later documents.

## Documents

- `spec/requirements.md`: Requirements driving the design, not necessarily complete.
- `spec/glossary.md`: Defines all terms used across the specification.
- `spec/model.md`: Subsystems, domain objects, database schemas.
- `spec/behavior.md`: Processes operating on the model.
- `spec/scenarios.md`: Scenarios and edge cases the design must handle.
- `spec/issues.md`: Outstanding questions and deferred decisions.

## Universal Checks

- What happens when two users perform an action simultaneously?
- What happens when the action is retried after a partial failure?
- What happens when the input exceeds expected size or volume limits?

## Conditional Checks

- A workflow that changes configuration: verify it includes an audit trail step
- A change to any external API contract: verify backward compatibility with the previous version is addressed
- A workflow that calls an external service: specify timeout, retry, and fallback behavior
```

Note: the example above includes a dedicated glossary, a Universal Checks section, and a Conditional Checks section, all of which are optional. A specification without a glossary would omit that entry, and terms would be introduced progressively in the documents themselves.

### Init

The `/spec-writing init` subcommand creates a SPECFILES.md manifest in the working directory and optionally creates starter spec files. If SPECFILES.md already exists, say "SPECFILES.md already exists. No changes needed." and stop — do not run any other part of this skill, do not verify or check anything, and do not proceed further.

Ask whether the user already has a specification file or directory.

- **Single file:** Read the file, create SPECFILES.md with a Documents section listing that file and a description based on its content. Include the document-order note shown in the example above.
- **Directory:** Read the files in the directory, organize them into reading order (least-dependent first), and create SPECFILES.md with a Documents section listing each file with a description. Include the document-order note.
- **No existing spec:** Ask for the directory name to use (e.g. `spec/`). Create the directory with stub files matching the example above — one per document entry, each containing a heading and a brief description of the file's purpose. Replace `spec/` in the example paths with the user-provided directory name. Then create SPECFILES.md with a Documents section listing them in the same order. Include the document-order note.

## Planning Phase

Before writing any changes, build a complete picture of what needs to change and where.

### 1. Read Before Writing

At minimum, always read:

- **Requirements files (if any exist)** — to verify the change against all requirements and constraints
- **Where terms are defined** — the file containing the glossary if one exists, or the early documents in reading order — to ground yourself in established vocabulary
- **The file(s) you plan to modify** — to understand current content and structure
- **The issues section (if one exists)** — to check whether the topic has outstanding questions

For non-trivial changes, also review the conditional checks in SPECFILES.md (if present) and read any files they suggest may be affected.

### 2. Map the Change

Identify every document and section that the change touches. Ask yourself:

- Does this introduce a new term? → It needs a definition where terms are defined, placed before its first use in the reading order.
- Does this introduce or depend on a concept that hasn't been explained yet? → The explanation must appear before any section that relies on it, whether or not the concept is elevated to a named term.
- Does this require new or changed data structures, fields, or types? → Update the data model sections.
- Does this change a process, workflow, or algorithm? → Update the relevant procedural sections and pseudocode.
- Does this remove existing content? → Trace all references across spec files and remove or update them. Clean up any terms, scenarios, or issues that exist only because of the removed content.
- If SPECFILES.md includes conditional checks, do any apply? → Follow each applicable check.
- Are there decisions I'm deferring or questions I can't answer yet? → Plan entries in the issues section.
- Does this resolve any existing issue? → Plan to remove it.

### 3. Verify Requirements Compliance

If requirements files exist, apply the verification described in Requirements Awareness. For each requirement, ask:

- Does the planned change satisfy this requirement, or is it neutral with respect to it?
- Could the planned change violate, weaken, or contradict this requirement?

### 4. Check Terminology and Concept Dependencies

Before writing, confirm that the terms you plan to use are already defined and that the concepts your new content depends on are already explained. For named terms being introduced:

- Draft the definition following the existing style
- Determine where it belongs in the reading order — it should appear after terms it depends on and before terms that depend on it (see Terminology Management for placement by mode, Progressive Explanation for ordering constraints)
- Verify the new term doesn't conflict with or duplicate an existing term

For unnamed concepts that later content will rely on, confirm that explanations will precede all dependent sections (see Progressive Explanation).

### 5. Present the Plan

When presenting a plan, be explicit about:

- Which files will be modified and why
- Any new terms or concepts being introduced, and where they will be placed in the reading order
- Any requirements inconsistencies found and how they will be resolved (or flagged for user decision)
- Any edge cases or issues identified
- Any questions or deferred decisions

## Writing Phase

### Terminology Discipline

- Use defined terms exactly as they appear where they were defined. Don't introduce synonyms or variant phrasings for established concepts.
- When you reach for a word that isn't already defined, check whether an existing defined term covers the concept.
- If a genuinely new term is needed, define it where terms are defined (see Terminology Management) before using it elsewhere.

### Progressive Explanation

This applies to all concepts, not just named terms: any idea that a reader must understand before later material makes sense should be explained first.

- When adding a term definition, place it after all terms it depends on and before all terms that depend on it.
- When introducing a concept — even one without a named term — verify that the explanation appears before any section that depends on it, both within the same document and across the document ordering.
- When adding a section to any document, verify it doesn't reference concepts or mechanisms introduced later in the same document.
- Follow the reading order defined in SPECFILES.md: documents listed earlier should be readable without documents listed later.

**Local forward references.** A forward reference is acceptable when the definition follows immediately in the same logical unit — for example, stating that something is one of several named types and then defining each type in the lines that follow, or defining mutually recursive concepts together in a single cohesive block. The principle to avoid is _distant_ forward references, where the reader must navigate elsewhere to find a definition before continuing.

### Precision

- Specify the concrete mechanism rather than describing behavior abstractly. Instead of "the system handles this," describe what the system does — which structures it queries, what conditions it checks, what it writes and where.
- Use pseudocode for processes involving conditions, loops, or multiple steps. Follow the existing style, defaulting to left margin start, 4-space indentation, separate algorithms in separate code blocks.
- Reference specific field names, table names, and data structures from the data model sections rather than describing them generically.
- When precision isn't yet possible, mark it explicitly with a TBD comment and add a corresponding entry to the issues section.

### Consistency

- When you modify a description or behavior in one file, search the other spec files for related content and update it to match.
- When you add or change a structure in the data model, verify that procedural sections reference it correctly.
- When you modify pseudocode, verify it uses the names and structures from the data model.
- If SPECFILES.md includes conditional checks, consult them for project-specific verifications that may apply.

### Requirements Compliance

- Before writing content that describes a new behavior or mechanism, re-check whether it satisfies the relevant requirements. The planning phase should have caught major conflicts, but details often emerge during writing.
- If you discover a conflict while writing that wasn't caught during planning, stop and ask the user rather than quietly adjusting either the change or the requirement.
- When writing content near the boundary of a requirement — behavior that is technically consistent but could be read as stretching the requirement — note the relationship explicitly so the user can confirm the interpretation.

### Robustness

For every new behavior, consider what happens when:

- Multiple actors operate concurrently on overlapping data
- Stale or very old inputs arrive
- An operation partially succeeds
- The data an operation depends on was deleted or modified by another actor
- A process is interrupted partway through
- The system operates at an unusual scale (very large inputs, very many concurrent actors)

If SPECFILES.md includes universal checks, also consult them for domain-specific robustness questions.

Add relevant scenarios to the scenarios section (if one exists) using the existing format.

### Issues Tracking

**Organization.** Issues are grouped under topic headings by relatedness — issues that are likely to inform or constrain each other's resolution belong under the same heading. When adding or removing issues, reorganize headings (split, merge, rename) if the current grouping is no longer cohesive.

**Content.** When you defer a decision, immediately add it to the issues section under the appropriate topic. When you encounter a question you can't answer, add it rather than making an assumption or leaving the question implicit. When a change resolves an existing issue, remove it as part of the same change. Frame issues as concrete questions or problem statements, not vague concerns.

### Pseudocode Correctness

- When modifying pseudocode, verify that every branch handles all cases described in the surrounding prose.
- After modifying pseudocode, re-read the prose in the same section and confirm agreement.
- Verify that names in pseudocode match the data model sections.
- When pseudocode and prose disagree, flag the discrepancy and ask the user which is authoritative before resolving it.

## Verification Checklist

After completing a change, run through these checks before presenting it:

- All requirements and constraints are satisfied by the change, or inconsistencies have been explicitly resolved with the user
- All terms used are defined (in the glossary or at their point of introduction), and new definitions have been added where appropriate
- No synonyms or variant phrasings introduced for established terms
- All affected documents have been updated, not just the primary target
- Data model structures and procedural sections reference each other correctly
- Pseudocode is consistent with prose and with data model definitions
- New content doesn't depend on concepts — named or unnamed — introduced later in the reading order, unless the forward reference is resolved immediately in the same passage (see Local Forward References)
- Robustness has been considered — both the built-in checks and any universal checks in SPECFILES.md — and relevant scenarios added to the scenarios section
- Conditional checks from SPECFILES.md, if present, have been followed
- Deferred decisions and open questions are captured in the issues section
- Resolved issues have been removed from the issues section
- TBD markers are present where precision is deferred
