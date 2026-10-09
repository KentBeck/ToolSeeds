# ToolSeeds

Programming tools can be transmitted as prompts instead of finished software. ToolSeeds is a library of prompts that help AI coding assistants create or adapt programming tools.

A tool seed describes a tool's purpose and expected behavior so an assistant can build it in the context of your project. Unlike finished software, a seed can be adapted to your language, framework, and constraints.

## Tools

1. [callGraphBrowser](callGraphBrowser/prompt.md): an interactive, layered call-graph page for a project, with functions colored by operation family, hover highlighting of callers and callees, and a side panel for details. Published as an artifact.
2. [dependencyMap](dependencyMap/prompt.md): building blocks in layer bands and area regions, resolved through the compiler, with coupling and instability per block, cycles, layering violations and a free body diagram of the selected block. Published as an artifact.
3. [pipelineAnatomy](pipelineAnatomy/prompt.md): the CI/CD pipeline as a left-to-right DAG of jobs, needs and artifact flow, with median/p90 timings, the critical path, and a what-if that shows which jobs still run and what the release gate decides when one fails, is skipped or is cancelled. Published as an artifact.
4. [qualityGateReport](qualityGateReport/prompt.md): the project's own quality-gate score explained: verdict, budget bar, every finding, the complexity distribution and code growth over time, with a what-if that excludes findings but never missing evidence. Published as an artifact.
5. [featureParity](featureParity/prompt.md): a feature-by-client matrix for projects with several apps, SDKs or bots, where every cell is decided by an explicit rule and backed by file:line evidence traced through the shared cores, with imported parity audits diffed against the code, cross-client rollouts from history, and a what-if that shows which clients inherit a core change. Published as an artifact.
6. [turbulence](turbulence/prompt.md): churn against complexity for every file, after Michael Feathers' [Getting Empirical about Refactoring](https://www.stickyminds.com/article/getting-empirical-about-refactoring) and the [turbulence](https://github.com/chad/turbulence) gem, for any language: a log-log scatter with Danger Zone, Cowboy Code, Fertile Ground and Healthy Closure quadrants, a ranked Danger Zone, per-function drill-down, a treemap and a monthly time-lapse of files moving between quadrants. Published as an artifact ([example](turbulence/screenshot.png)).
7. [promptToDeterministicTool](promptToDeterministicTool/prompt.md): turns any seed above into a tool committed to the project, with a pure core, cached network data, precomputed layouts and what-ifs, and the seed's VERIFY steps as tests, so one command produces the same artifact every time.
8. [sightline](sightline/prompt.md): a findings button built into the application under development, for defects in what it produces as much as in its interface: it freezes the view, captures real pixels, takes numbered marks that resolve to the objects under them, and files them with the state, seeds, backend log and recent UI actions that produced the result, so the coding agent can rebuild it outside the UI, trace the marked objects to the algorithm or backend step that made them, and turn the finding into a failing check. Built into the project, not published as an artifact.

## Using a tool

Open a coding agent in the project you want the tool for, then paste in the tool's `prompt.md` along with any relevant project context or constraints. The agent builds the tool against your code. Review the result and run the project's tests.

## Creating a new tool

1. Make a directory named after the tool in camelCase, such as `myToolName/`.
2. Add a `prompt.md` file to it. Write the prompt so it works in any project:
   - Say what the tool is and what it produces.
   - Group the requirements under headings, for example DATA, LAYOUT, INTERACTION.
   - Be specific where a vague prompt would go wrong. Name the edge cases, the
     libraries, and the mistakes you've already seen an agent make.
   - End with a VERIFY step: what the agent must check before calling the job
     done, and what it should report back.
3. Run the prompt on at least one real project and revise it until the result
   is what you want.
4. Add the tool to the list above with a one-line description and a link to
   its `prompt.md`.
