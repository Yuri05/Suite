# AI/Agentic PBPK Model Setup Outline (OSP Suite)

This outline shows how a PBPK modeling expert can direct an AI/agentic workflow to assemble, refine, and qualify a PBPK model with Open Systems Pharmacology (OSP) tools such as PK-Sim®, MoBi®, and the OSPSuite R ecosystem.

Primary references:
- OSP documentation: https://docs.open-systems-pharmacology.org/
- PK-Sim documentation: https://docs.open-systems-pharmacology.org/working-with-pk-sim/pk-sim-documentation
- MoBi documentation: https://docs.open-systems-pharmacology.org/working-with-mobi/mobi-documentation
- PK-Sim CLI: https://docs.open-systems-pharmacology.org/working-with-pk-sim/pk-sim-documentation/pk-sim-command-line-interface
- Project snapshots (JSON) and PKML exchange: https://docs.open-systems-pharmacology.org/working-with-pk-sim/pk-sim-documentation/importing-exporting-project-data-models
- OSPSuite-R packages: https://www.open-systems-pharmacology.org/OSPSuite-R/

## Inputs the expert should hand to the agent
- Modeling intent and context: compound, indication, routes, target population(s), species, key PD/biomarker links, special populations, DDI questions.
- Data and evidence:
  - Clinical/PK datasets (CSV/Excel/NONMEM) for observed data import.
  - In vitro parameters (f_u, f_u,mic, solubility, permeability, intrinsic clearance, transporter kinetics).
  - Formulation details and administration protocols.
  - Expression data (tissue enzymes/transporters), population covariates, physiology references.
  - Known interactions (perpetrators/victims), concomitant meds, and mechanistic hypotheses.
- Model boundaries and assumptions: intended granularity, required observers, variability/uncertainty ranges, acceptable simplifications.
- Acceptance criteria: target metrics (AUC/Cmax ratios, visual predictive checks, GOF thresholds), qualification scenarios, reporting needs.
- Tooling constraints: OSPSuite/PK-Sim/MoBi version, R version, available CLI access, compute limits, locations of PKML/snapshots, folder structure for inputs/outputs.

## OSP toolchain to be orchestrated
- **PK-Sim® (GUI + CLI)** for building-block authoring (individuals, populations, compounds, formulations, protocols, events, expression profiles), simulation runs, and project snapshots (JSON).
- **MoBi®** for structural edits beyond PK-Sim (custom reactions, spatial structures, passive/active transports, modularization).
- **PKML / Project snapshots** as exchange and versionable artifacts; snapshots are convenient for agent-led diffs, PKML for cross-tool reuse.
- **OSPSuite R packages**:
  - `ospsuite` for loading/simulating PK-Sim/MoBi models, sensitivity analysis, and automation.
  - `ospsuite.parameteridentification` for Bayesian/likelihood-based fitting.
  - `tlf` for standardized tables/listings/figures.
  - `ospsuite.reportingengine` for automated evaluation and qualification reports.
- **Qualification/automation utilities**: PK-Sim CLI for batch runs, Qualification Runner or CLI pipelines for regression/qualification scenarios, Installation Validator when setting up a new machine.

## Iterative refinement loop (agent-assisted)
1. **Setup & alignment**
   - Verify toolchain (PK-Sim/MoBi, R packages, CLI availability); if needed, run Installation Validator.
   - Standardize directories for inputs (`data/observed`, `data/invitro`, `snapshots/`, `pkml/`) and outputs (`results/`, `reports/`).
   - AI role: summarize objectives and constraints; generate a task board with scenarios, metrics, and data gaps.
2. **Seed baseline PK-Sim model**
   - Create individuals/populations, compound(s), formulation(s), protocols, observers, and expression profiles in PK-Sim.
   - Import observed data (CSV/Excel/NONMEM) and map units/columns.
   - Run initial simulations; export snapshot (JSON) and PKML for traceability.
   - AI role: propose default building-block settings, unit checks, and initial parameter priors based on literature.
3. **Automate runs & comparisons**
   - Use PK-Sim CLI to batch-run key scenarios from the snapshot.
   - Load PKML in R with `ospsuite` to script simulations and produce quick diagnostics (GOF plots, residuals).
   - AI role: generate/modify CLI command lines and R snippets for simulation/plotting, flag failed runs.
4. **Parameter refinement**
   - Use `ospsuite.parameteridentification` to fit to observed data; apply sensitivity analysis to prioritize parameters.
   - Update snapshot/PKML after parameter changes; version control the artifacts.
   - AI role: suggest bounds/priors, interpret fit diagnostics, recommend next experiments or data needs.
5. **Structural refinement (MoBi)**
   - Move to MoBi for custom pathways (reactions, transporters, disease modules) or modularization.
   - Round-trip updated PKML back to PK-Sim when applicable; keep snapshots aligned.
   - AI role: draft reaction network modifications, validate units/stoichiometry, and propose observer placements.
6. **Qualification & regression**
   - Assemble qualification scenarios (e.g., populations, routes, DDIs) and run via PK-Sim CLI or Qualification Runner.
   - Use `ospsuite.reportingengine` + `tlf` to generate standardized reports (VPCs, GOF tables).
   - AI role: map qualification plan to executable scenarios, monitor diffs across iterations, summarize pass/fail items.
7. **Documentation & hand-off**
   - Persist artifacts: snapshot, PKML, parameter ID projects, R scripts, report outputs, and run logs.
   - Capture model assumptions, limitations, and open questions; tag data sources and provenance.
   - AI role: compile changelog, draft report narrative, prepare a “how to rerun” checklist (CLI/R).

## AI/agent collaboration patterns
- **Input curation agent**: normalizes experimental datasets, checks units, and builds mapping templates for observed data import.
- **Modeling copilot**: drafts PK-Sim CLI commands, R scripts (ospsuite, parameteridentification, reportingengine, tlf), and MoBi change lists.
- **Critic/evaluator**: compares simulated vs observed (AUC/Cmax, VPCs), highlights failing scenarios, and prioritizes next fixes.
- **Knowledge steward**: updates assumptions, decisions, and rationale after each iteration; tracks snapshot/PKML versions.
- **Orchestrator**: sequences steps (simulate → compare → adjust), ensures artifacts are versioned, and triggers regression suites.
