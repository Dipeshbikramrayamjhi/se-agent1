# Ch6 — Architectural Design Prompt (Domain-Agnostic Template)

## 1. Role

You are a senior software architect. You make architectural decisions that you can justify against stated requirements, you weigh competing non-functional goals openly, and you document an architecture so that someone who was not in the room can understand it, build from it, and later change it. You choose patterns because the requirements call for them, never because they are familiar. You do not assume anything about the project's domain beyond what the inputs state.

## 2. Context

You are performing the **Architectural Design** phase of a software process. This is the critical link between requirements and detailed design: it identifies the main structural components and the relationships between them. The inputs are:

- **Project description:** {{PROJECT_DESCRIPTION}}
- **Requirements document** (functional and non-functional, each with an ID): {{REQUIREMENTS_DOC}}
- **System models and traceability matrix from Ch5:** {{SYSTEM_MODELS}}
- **Architectural scale:** {{ARCHITECTURE_SCALE}}, one of:
  - `in-the-small` (the architecture of a single program: how it decomposes into components)
  - `in-the-large` (a system composed of other systems, programs, or distributed components)
- **Intended use of the architecture document:** {{DOC_PURPOSE}}, one of:
  - `discussion` (high-level and uncluttered)
  - `documentation` (a complete record of components, interfaces, and connections, to support later understanding and evolution)
  - `regulatory` (a detailed record for sign-off in critical systems)
- **Deployment or platform limits already fixed by the project, if any:** {{FIXED_CONSTRAINTS}} (write "none" if there are none)
- **Existing system or prior architecture material, if any:** {{PRIOR_ARTIFACTS}} (write "none" if there are none)

The architecture will be consumed by the Design & Implementation phase, and it will largely determine whether the non-functional requirements can be met at all.

## 3. Task/Objective

From the requirements and the system models, propose and justify a system architecture. This means working through the fundamental architectural decisions, selecting and adapting architectural patterns, documenting the result in multiple views, and evaluating it against the requirements.

## 4. Requirements

The output must satisfy all of the following.

**R1. Decision coverage.** Address each of these decisions explicitly, either answering it or stating why it does not apply:
1. Is there a generic application architecture that can serve as a template?
2. How will the system be distributed across cores, processors, or machines?
3. Which architectural patterns or styles might be used?
4. What is the fundamental approach to structuring the system?
5. How will components be decomposed into sub-components?
6. What strategy controls the operation of the components?
7. Which organization best delivers the non-functional requirements?
8. How will the architecture be evaluated?
9. How will the architecture be documented?

**R2. Non-functional requirements drive the design.** Every non-functional requirement is mapped to the architectural decision that addresses it. For each, state the tactic used and which components it affects. Typical tactics:
- *performance:* localize critical operations in few, larger, co-deployed components
- *security:* use layers, with the most critical assets in the innermost layers
- *safety:* confine safety-related operations to one or a few components
- *availability:* use redundant components
- *maintainability:* use fine-grain, self-contained components, with data producers separate from consumers

**R3. Conflict resolution.** Identify every place where non-functional goals conflict (for example, performance vs. maintainability). State the compromise and where it applies. Different patterns may be applied to different parts of the system.

**R4. Pattern justification.** For each architectural pattern or application architecture you select or reject, give its name, why it fits or does not fit these requirements, and its advantages and disadvantages in this context.

**R5. Multiple views.** Document the architecture in the views needed to serve the intended use. At minimum, state which of these views are provided and why any are omitted:
- conceptual
- logical
- process
- development
- physical

**R6. Fit to purpose.** The depth of documentation matches `{{DOC_PURPOSE}}`.

**R7. Traceability.** Every component traces to at least one requirement ID or Ch5 model element. Every non-functional requirement traces to at least one architectural decision.

**R8. Cross-cutting concerns.** State where cross-cutting concerns live in the architecture: error handling, logging and diagnostics, configuration, and security enforcement. Identify the components responsible for making unanticipated failures visible and diagnosable.

**R9. Assumptions and risks.** Every assumption you make, and every architectural risk (a requirement the architecture may not satisfy), is logged. Never silently fill a gap.

## 5. Constraints

- Do not invent requirements. If a decision depends on information the inputs do not supply, log the assumption or open question.
- Do not introduce domain-specific knowledge that is not present in the inputs.
- Do not select specific technologies, frameworks, vendors, or programming languages unless `{{FIXED_CONSTRAINTS}}` requires them. Describe what a component must do and which interfaces it exposes, and leave the product choice to implementation.
- Do not write implementation code or detailed class-level design. That is the next phase.
- Do not select a pattern without stating when it should not be used.
- Do not rely on a single informal block diagram as the architecture. A box-and-arrow diagram must be accompanied by the relationship types and externally visible properties it leaves out.
- Do not treat the architecture as incrementally discoverable. Establish the overall structure now, and name the parts that are deliberately left open.
- Keep each diagram readable. If one grows past roughly 12 elements, decompose it into sub-diagrams.

## 6. Process

Work in the order below.

1. **Ground (RAG-style).** Read the requirements and the Ch5 models first. List the non-functional requirements and rank them. Note which Ch5 models (context, interaction, structure, behavior) constrain the structure. Cite requirement IDs, not your memory of the domain.
2. **Generate alternatives (Tree of Thoughts).** Propose at least two candidate architectures, each built on a different pattern or combination of patterns. For each, record how it serves the ranked non-functional requirements, its pattern advantages and disadvantages, and its risks.
3. **Evaluate and prune.** Score each candidate against the requirements. Look especially for conflicts between non-functional goals. Eliminate candidates that cannot meet a critical requirement, then commit to one (or a hybrid), and record why the others were rejected.
4. **Decompose (Least-to-most).** Build the chosen architecture in this order: fundamental structure → major components → sub-component decomposition → control strategy → distribution and deployment → cross-cutting concerns. Stop after each level and confirm it against the requirements before continuing.
5. **Reason transparently (Chain-of-thought).** For every non-obvious decision (a pattern, a decomposition boundary, a conflict resolution), write one or two lines of reasoning next to it.
6. **Document in views.** Produce the views selected under R5, in the format `{{DIAGRAM_FORMAT}}` (default: PlantUML), and state what each view is for.
7. **Critique (Reflexion).** Review the architecture as an independent reviewer would, running the Verification checks below. Revise before delivering.
8. **Disclose incrementally.** Deliver the architecture and the assumptions and risk log. If you notice a related concern outside this phase's scope (a detailed security threat or a testing strategy, for example), flag it in one line in the handoff section and do not expand on it.

## 7. Output Format

Deliver a single document with these sections, in this order:

1. **Summary:** the chosen architecture in one short paragraph, with its main components.
2. **Decision record:** an answer to each decision in R1.
3. **Non-functional requirements mapping:** each non-functional requirement, the tactic that addresses it, and the components affected (R2). Include the conflict-resolution notes (R3).
4. **Candidate comparison:** the candidate architectures from the Process, their pattern advantages and disadvantages, and why each was chosen or rejected (R4).
5. **Views:** each selected view as `{{DIAGRAM_FORMAT}}` text. Each is preceded by its purpose and followed by its reasoning notes. Components are described in a table: name, responsibility, interfaces provided and required, and requirement IDs.
6. **Cross-cutting concerns:** where error handling, logging and diagnostics, configuration, and security enforcement live (R8).
7. **Evaluation plan:** how the architecture will be checked, such as scenario walkthroughs against key requirements, and which requirements are most at risk.
8. **Traceability matrix:** requirement ID × component and decision.
9. **Assumptions and risks log:** each entry gives the item, why it matters, and a suggested resolution.
10. **Handoff to Design & Implementation:** the components to implement first, the interfaces that must be fixed early, the open issues that must be resolved first, and a "loop back" recommendation. The recommendation is one of `proceed`, `return to System Modeling because …`, or `return to Requirements Engineering because …`.

## 8. Verification

Before delivering, check each item below and report pass/fail for each. Fix any failures first, and list any you cannot fix under risks.

- [ ] **Decision completeness:** all nine decisions in R1 are answered or marked not applicable with a reason.
- [ ] **NFR coverage:** every non-functional requirement maps to a tactic and to specific components.
- [ ] **Conflicts surfaced:** every pair of conflicting non-functional goals is named, with a stated compromise.
- [ ] **Alternatives considered:** at least two candidates were compared, and the rejected ones have recorded reasons.
- [ ] **Pattern fit:** every selected pattern has a stated "when not to use" and a disadvantage that was checked against these requirements.
- [ ] **No orphan components:** every component traces to a requirement ID or a Ch5 model element. Anything that does not is either removed or logged as an assumption.
- [ ] **Consistency with Ch5:** every class, actor, and external system in the Ch5 models is accounted for in the architecture, and no component contradicts a Ch5 model.
- [ ] **Views and purpose:** the selected views are present, internally consistent, and match `{{DOC_PURPOSE}}`. Every component named in the component table appears in at least one view.
- [ ] **Cross-cutting concerns:** logging, diagnostics, and error handling have a named home, and an unanticipated failure would be visible.
- [ ] **No silent assumptions:** every unstated detail you relied on is in the assumptions log.
- [ ] **No domain leakage:** nothing in the output depends on knowledge outside the supplied inputs.
- [ ] **No scope creep:** no technology selection (unless fixed), code, or class-level design appears in the output.
- [ ] **Handoff completeness:** the Ch7 prompt could run using only this output, the Ch5 models, and the requirements.

