# UI Context: [FEATURE OR PAGE NAME]

**Contract version**: 1.0
**Status**: [Validated; contract validation status, separate from context readiness]
**Reference**: [R-IDs and input summary]
**Repository**: [Project root; app scope; revision or no Git; local modifications]
**Target**: [Feature or UI area; existing feature path or staging]
**Generated / refreshed**: [Timestamp with timezone]
**Template**: [Resolved template source/composition]
**Derived from**: [Prior context path/version, or Not applicable]

<!-- Authoring instructions: replace every placeholder and remove instructional
comments. Retain all nine section headings. Use explicit Unknown / Not observed /
Not applicable with reasons. Optional subsections may be omitted only when
irrelevant. Source paths are relative to the project root, never to this file.
Every material fact cites an evidence ID resolved in section 9. Write prose in
the user's language unless requested otherwise. Use stable R-* reference, K-*
repository, UI-* region, C-* conflict, A-* assumption, and Q-* question IDs. -->

## 1. Reference Overview

### Sources

| ID | Source | Type | Scope | Inspection and coverage |
|----|--------|------|-------|-------------------------|
| R-01 | [Root-relative path or sanitized URL] | [Image / local HTML / URL] | [Area] | [Actual method; Full / Partial / Unavailable; limitations] |

### Intended UI

[Visible purpose, page boundaries, user-stated intent and its provenance.
Separate what the reference shows from what the user requests.]

## 2. UI Structure

| Region ID | Region / Parent | Role | Layout Characteristics | Evidence and classification |
|-----------|-----------------|------|------------------------|-----------------------------|
| UI-01 | [Region / parent ID or root] | [Purpose] | [Observed relationships] | [R-ID + observed / measured / inferred] |

[Describe visual hierarchy, major relationships, and only responsive observations
at actually inspected viewports. Identify unknown layouts.]

## 3. Visual Characteristics

| Category | Reference Characteristics | Repository Alignment | Evidence | Classification / Conditions |
|----------|---------------------------|----------------------|----------|-----------------------------|
| Typography | [Finding or unknown] | [Verified convention or unknown] | [R/K-IDs] | [Observed / measured / declared / inferred; conditions] |
| Colors | [Finding or unknown] | [Tokens or conflict ID] | [R/K-IDs] | [Classification] |
| Spacing | [Finding or unknown] | [Convention or unknown] | [R/K-IDs] | [Classification] |
| Layout | [Finding or unknown] | [Convention or unknown] | [R/K-IDs] | [Viewport / scale] |
| Borders / Effects | [Finding or unknown] | [Convention or unknown] | [R/K-IDs] | [Classification] |
| Icons / Images | [Finding or unknown] | [Resource or gap] | [R/K-IDs] | [Classification] |

<!-- Image pixel estimates are not CSS dimensions or confirmed design tokens.
Record source declarations separately from rendered/computed measurements. -->

## 4. Behavior and UI States

| Element / Region ID | Observed Behavior / State | Evidence and Conditions | Product Intent | Unknowns |
|---------------------|---------------------------|-------------------------|----------------|----------|
| [Element / UI-ID] | [Visible or safely observed state] | [R-ID; method, viewport, action if inspected] | [User-stated / demonstration only / unknown] | [Q-ID or explicit gap] |

[Relevant loading, empty, error, validation, focus, keyboard, navigation, and
responsive state gaps. Do not invent states or hidden business rules.]

## 5. Codebase Alignment

### Relevant Frontend Context

- Framework and dependencies: [Evidence K-ID; declared vs locked version]
- Existing layout conventions: [Evidence K-ID or scoped absence]
- Design system and tokens: [Evidence K-ID; theme/variants]
- Styling approach: [Evidence K-ID]
- Relevant routing and state conventions: [Evidence K-ID or not applicable]
- Applicable project / Constitution constraints: [Source and constraint]
- Existing feature intent / decisions: [spec.md / plan.md evidence and status, or absent]
- Inspected app and shared package boundaries: [Paths and limits]

### Component Mapping

| Reference Element / UI-ID | Existing Component | Source Path / Symbol | Suitability Evidence | Status / Gap |
|---------------------------|--------------------|----------------------|----------------------|--------------|
| [Element / UI-ID] | [Component or none] | [Root-relative path; locator] | [K-ID; relevant API/style/usage] | [Verified / Candidate / None in inspected scope] |

### Reusable Assets (optional)

| Reference Asset | Existing Asset | Source / Evidence | Status / Gap |
|-----------------|----------------|-------------------|--------------|
| [R-ID; asset] | [Asset or none] | [Path + K-ID] | [Verified / Candidate / Missing / Unknown] |

## 6. Conflicts and Decisions Needed

| ID | Issue and Impact | Reference / Intent Evidence | Repository / Feature Evidence | Status / Decision Provenance |
|----|------------------|-----------------------------|---------------------|------------------------------|
| C-01 | [Conflict; affected UI-IDs] | [R-ID or user instruction] | [K-ID] | [Open / Resolved / Superseded; decision, who/when, evidence] |

[Explicitly say if no conflicts were found in the inspected scope. Proposed
options are not accepted decisions. Preserve user-authored decisions on refresh.]

## 7. Assumptions and Unknowns

### Assumptions

| ID | Assumption | Rationale / Evidence | Confidence / Impact | Status |
|----|------------|----------------------|---------------------|--------|
| A-01 | [Reversible assumption] | [Reason + evidence] | [Confidence; effect] | [Open / Confirmed by user / Superseded] |

### Open Questions

| ID | Question | Evidence / Gap | Impact / Affected Stage | Status |
|----|----------|----------------|------------------------|--------|
| Q-01 | [Question] | [R/K/C-IDs or missing evidence] | [Core blocking / stage blocking / nonblocking] | [Open / Answered; answer provenance] |

### Preserved User Notes and Decisions (optional)

[Retain existing user-authored content and flag stale evidence without discarding
the decision. Keep earlier context provenance when refreshing or associating.]

## 8. Downstream Guidance

### For Specify

[Reference purpose, user-visible intent, supported behavior and functional
questions with IDs. State which observations still require requirement approval.]

### For Plan

[Verified repository constraints, reusable capabilities, visual/resource gaps,
and decisions to resolve against the approved spec. Recheck source freshness.]

### For Tasks / Implementation

[UI region IDs and later verification considerations. Identify accepted spec/plan
decisions if available; otherwise state the prerequisite without fabricating
approval. No task list or code. Context cannot approve implementation.]

### Explicit Consumption

[Concrete artifact path to pass to core commands. Registration does not make core
commands automatically read this artifact.]

## 9. Evidence and Coverage

### Inspection Capabilities

| Capability | Actual Provider | Availability / Scope | Selected Method or Fallback | Evidence Limitation |
|------------|-----------------|----------------------|-----------------------------|---------------------|
| [Required operation] | [Callable tool/skill or none] | [Available / Unavailable / Restricted / Unverified; access scope] | [Actually used method or no safe fallback] | [Effect on reference/repository coverage] |

### Evidence Register

| ID | Source / Locator | Supports | Method / Classification | Version and Inspection Conditions |
|----|------------------|----------|-------------------------|-----------------------------------|
| R-01 | [Path + image region / HTML selector or lines / URL locator] | [UI-IDs and claims] | [Observed / measured / declared] | [Hash if available; timestamp; image dimensions or viewport; rendering/script mode] |
| K-01 | [Root-relative current file + symbol or lines] | [Constraint or mapping] | [Source verified / declared] | [Revision and dirty-state caveat; timestamp] |

<!-- Add evidence rows for user instructions/project policies as needed; use
stable IDs with a clear classification. Confidence is separate from provenance.
Record hashes when available and local modifications rather than attributing
dirty files to HEAD. For URLs, record requested/final source and access state;
redact credentials and sensitive query parameters. OCR-only image inspection
and static-only HTML inspection do not establish full rendered visual coverage. -->

- References successfully inspected: [IDs and actual scope]
- References unavailable or partially inspected: [IDs, reasons, missing resources]
- Repository paths verified: [Paths, app scope, relevant shared packages]
- Areas not examined: [Boundaries and why]
- Material unresolved questions: [C/Q-IDs, or explicit none within scope]
- Freshness / refresh changes: [Revisions, changed findings, superseded evidence]

### Readiness Gates

<!-- Store the gate results evaluated by the command's Context Quality Rules. -->

| Gate | Result | Evidence / Issue IDs and Reason |
|------|--------|--------------------------------|
| G1 Target | [Pass / Gap / Fail] | [Scope and destination] |
| G2 Reference | [Pass / Gap / Fail] | [Appropriate inspection and input coverage] |
| G3 Repository | [Pass / Gap / Fail] | [Current source verification and scoped coverage] |
| G4 Integrity | [Pass / Gap / Fail] | [Contract, traceability, conflict/decision preservation] |
| G5 Decisions | [Pass / Gap / Fail] | [Context usability and affected-stage C/Q-IDs] |

**Context readiness**: [Ready / Conditional / Blocked; gate-derived reason]

| Downstream Stage | Readiness | Required Resolution |
|------------------|-----------|---------------------|
| Specify | [Ready / Conditional / Blocked] | [Functional questions / none] |
| Plan | [Ready / Conditional / Blocked] | [Constraints/conflicts; approved spec prerequisite] |
| Tasks / Implement | [Requires approved spec/plan] | [Accepted decisions and later UI validation] |

<!-- Context readiness records evidence usability. Hook execution outcomes belong
in the completion report; neither status establishes finished UI acceptance. -->
