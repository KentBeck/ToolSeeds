Prompt for a cross-boundary binding-picture tool:

Build me a small, interactive picture of the relationships that matter to one
concrete change or usage question in this project. Show what the available
sources establish, why each relationship may matter, and what remains unknown.
Publish it as an inspectable artifact. This is not a whole-system dependency
map or a new architecture-analysis engine.

QUESTION AND BOUNDARY
- Use the supplied question and boundary. If none is supplied, select one
  concrete cross-component change or usage question supported by this project's
  documentation, and state that you selected it. Do not invent requirements.
- Define the selected side of the boundary and its known counterparts. Use
  explicit ownership or architecture records when available. Otherwise label
  the scope as a provisional technical boundary; a repository or folder is
  not automatically a bounded context or team boundary.
- Examine only relationships relevant to that question, in both directions.
  Do not claim to have inventoried the whole system. If there is no defensible
  cross-component boundary, explain why and stop rather than drawing one.

SOURCES AND EVIDENCE
- Reuse the project's available tools and records: source code, configuration,
  interface/event contracts, architecture documents, and existing runtime or
  deployment captures. Do not require every source type or install new
  monitoring infrastructure. Keep inspected systems read-only; write only
  investigation outputs.
- If a read-only architecture-knowledge provider is available (for example,
  through an API or MCP), use its qualified answers as supplied. Preserve its
  exact statuses, reasons, snapshot identifiers, evidence references and
  limitations. Do not independently recreate or reinterpret its assessment
  rules. The investigation must remain useful without a provider.
- Record which repositories/revisions, configurations, captures and provider
  answers were actually inspected. For each factual finding, link to a
  specific file/line, captured record, or provider evidence reference. Resolve
  references when possible and label unresolved ones. Record environment and
  observation time where relevant; missing input is not negative evidence.

BINDINGS AND MEANING
- For each relevant relationship, show the selected-side participant, counterpart,
  direction, interface or destination, and supporting evidence. Include direct
  calls, messages or shared resources only when supported by the inputs.
- Distinguish what source code references, what a contract or configuration
  declares, what a runtime capture observes, and what an external provider
  qualifies. An explicit owner promise may be shown as a promise; an ordinary
  schema or document is not automatically one. Source findings beyond a
  provider's coverage are additional findings, not changes to its
  assessment. Do not merge different evidence kinds into a universal status.
- Resolve identities using explicit mappings or traceable implementation and
  configuration paths, not similar names. An unresolved association stays
  unresolved. Do not infer execution, delivery, completeness or absence from
  a declaration, a code reference, or a single observation.
- Explain briefly why a binding might matter to the question, naming its
  supporting premises and what should be checked next. Mark this as
  interpretation, never as independently established evidence.

PRESENTATION
- Put the question and selected boundary first. Show a compact inbound /
  outbound binding list or table (including internal or shared dependencies
  when relevant), with evidence kinds and unknowns visible. Prefer this over
  a large node-link graph.
- Selecting a binding shows its exact references, relevant source entry
  points where available, applicable revision/time/scope, provider findings if
  present, limitations, and the next question to investigate. Separate facts
  from interpretations; make source links usable from the published artifact.
- Provide a simple way to filter by direction or unresolved status. Make
  the view readable without relying on color alone, and include a visible
  list of unavailable or incomplete inputs. Use the project's normal artifact
  mechanism; otherwise produce self-contained HTML.
- If a provider can be disabled, remove its claims from EVERY visible surface:
  binding rows, explanations, summaries, unresolved items, evidence details,
  and verification logs. Keep independently supported source findings.
  Hiding claims is not a security boundary for embedded data.

VERIFY, THEN PUBLISH
- Check that every displayed factual claim has an inspectable reference.
  Cross-check at least three different bindings directly against the inputs,
  or all if fewer; include an unresolved or ambiguous case when available.
  Verify the stated boundary and direction against the underlying sources.
- When a qualified provider is used, compare the displayed findings with the
  provider's response; do not strengthen its conclusions. If provider-off
  behavior exists, check its rendered output, including explanations and logs.
- Open the artifact and test binding selection, one filter, and evidence
  navigation when possible. Distinguish executed tests from static review;
  never claim an unperformed interaction was tested.
- Tell me the question, inspected sources and revisions, relevant bindings,
  one decision-relevant insight if supported, unresolved questions, and the
  checks performed. Do not describe an unverified suggestion as a finding.
