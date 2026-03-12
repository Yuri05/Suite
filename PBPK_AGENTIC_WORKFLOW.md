# Setting Up a PBPK Model in OSP Using AI / Agentic Workflows

This guide provides a detailed outline for a PBPK (Physiologically Based PharmacoKinetics) modeling expert who wants to establish and refine a PBPK model in the Open Systems Pharmacology (OSP) Suite using AI-assisted and agentic workflows.

---

## Table of Contents

1. [Overview](#overview)
2. [Required Inputs](#required-inputs)
3. [Model Refinement Iterations](#model-refinement-iterations)
4. [OSP Tools and Their Roles](#osp-tools-and-their-roles)
5. [AI Tools and Integration](#ai-tools-and-integration)
6. [End-to-End Agentic Workflow Example](#end-to-end-agentic-workflow-example)
7. [References](#references)

---

## Overview

A PBPK model describes drug absorption, distribution, metabolism, and excretion (ADME) in a mechanistic, physiology-based manner. Setting up such a model in OSP typically involves:

1. **Collecting drug and system data** from literature, databases, and in vitro experiments.
2. **Building an initial (bottom-up) model** using PK-Sim® with physicochemical and in vitro parameters.
3. **Iteratively refining** the model against observed in vivo PK data through parameter identification and sensitivity analysis.
4. **Extending and customizing** the model in MoBi® for mechanistic complexity (e.g., target-mediated drug disposition, metabolite kinetics).
5. **Automating reporting and qualification** using R packages and the qualification framework.

AI and agentic workflows can accelerate and semi-automate each of these steps by extracting data from literature, generating R scripts, interpreting simulation results, and orchestrating tool interactions.

---

## Required Inputs

### 1. Drug Physicochemical Properties

| Property | Description | Source |
|---|---|---|
| Molecular weight (MW) | Molecular weight in g/mol | ChemDraw, PubChem, literature |
| Lipophilicity (logP / logD) | Partition coefficient (pH-dependent if ionizable) | Measured or predicted |
| Aqueous solubility | pH-dependent solubility profile | Measured |
| pKa values | Acid/base dissociation constants | Measured or predicted |
| Fraction unbound in plasma (fu) | Plasma protein binding | Measured in vitro |
| Blood-to-plasma ratio (B/P) | Drug distribution between blood cells and plasma | Measured in vitro |
| Permeability (Peff) | Intestinal effective permeability | Measured (Caco-2, PAMPA) or predicted |

### 2. In Vitro ADME Data

| Property | Description | Source |
|---|---|---|
| Hepatic intrinsic clearance (CLint) | Metabolic stability in microsomes or hepatocytes | Measured in vitro |
| CYP/UGT enzyme kinetics (Km, Vmax) | Enzyme-specific metabolic rates | Measured in vitro |
| Transporter kinetics | Influx/efflux transporter data (Pgp, BCRP, OATPs, etc.) | Measured in vitro |
| Plasma/tissue binding | Fraction unbound in tissues | Measured or predicted |

### 3. In Vivo PK Data (Observed Data)

| Data Type | Description | Format |
|---|---|---|
| Plasma concentration-time profiles | IV and/or oral PK profiles from clinical/preclinical studies | Excel, CSV |
| Non-compartmental PK parameters | AUC, Cmax, t½, CL, Vd | Summary tables |
| Urine data | Renal excretion fractions | Optional, if relevant |
| Tissue concentration data | For PBPK model refinement | Optional |

### 4. Dosing and Study Design Information

- Route of administration (IV, oral, SC, etc.)
- Dose amount and units
- Dosing frequency (single dose, multiple dose)
- Population characteristics (species, age, sex, body weight, disease state)
- Formulation details (solution, tablet, capsule — including dissolution characteristics for oral formulations)

### 5. Physiological and Systems Parameters

PK-Sim® contains built-in databases with physiological parameters for humans and common laboratory animals. Customizations may include:

- Specific organ volumes and blood flows for special populations
- Enzyme expression levels (from gene expression databases included in PK-Sim®)
- Transporter expression profiles (via [Expression Profiles](https://docs.open-systems-pharmacology.org/working-with-pk-sim/pk-sim-documentation/pk-sim-expression-profile) building block)

---

## Model Refinement Iterations

PBPK model development follows an iterative cycle. AI agents can assist at each stage.

### Iteration 0 — Initial Bottom-Up Model Setup

**Goal:** Build a first-principles model using only in vitro/physicochemical data, with no fitting to in vivo data.

**Steps:**
1. Create a **Compound** building block in PK-Sim® with all physicochemical and in vitro parameters.
2. Create an **Individual** or **Population** building block for the target species/population.
3. Add **Expression Profiles** for relevant metabolizing enzymes and transporters.
4. Define an **Administration Protocol** matching the study design.
5. Set up an initial **Simulation** and run it.
6. Export results and compare visually against observed data.

**AI Assistance:**  
- An AI agent can extract physicochemical and in vitro parameters from literature PDFs and populate a structured input template.
- An AI agent can generate the initial R script using `{ospsuite}` to load, run, and plot the simulation.

**Acceptance Criteria:**  
- Simulated PK profiles are within 2-fold of observed data for key PK metrics (Cmax, AUC, t½).
- If criteria are not met, proceed to Iteration 1.

---

### Iteration 1 — Sensitivity Analysis and Parameter Identification

**Goal:** Identify which parameters drive model-data discrepancies and refine them against in vivo PK data.

**Steps:**
1. Run a **local sensitivity analysis** using `{ospsuite}` to rank parameters by their influence on key PK outputs (AUC, Cmax, t½).
2. Use `{ospsuite.globalsensitivity}` for a global sensitivity analysis if needed.
3. Use `{ospsuite.parameteridentification}` to fit the most influential parameters (e.g., CLint scaling factor, fu scaling, permeability) against observed plasma concentration-time data.
4. Constrain parameter bounds to physiologically plausible ranges.
5. Re-run the simulation with optimized parameters and evaluate goodness-of-fit.

**AI Assistance:**  
- An AI agent can interpret sensitivity analysis output and propose a rational parameter subset for identification.
- An AI agent can generate the R script for `{ospsuite.parameteridentification}` configuration and execution.
- An AI agent can evaluate convergence diagnostics and suggest adjustments to the optimization algorithm settings.

**Acceptance Criteria:**  
- Optimized simulations reproduce observed data within 2-fold for AUC and Cmax.
- Optimized parameter values remain within physiologically plausible ranges.
- If complex kinetics are suspected, proceed to Iteration 2.

---

### Iteration 2 — Mechanistic Model Extension in MoBi®

**Goal:** Add mechanistic complexity that cannot be captured in PK-Sim® alone.

**Examples:**
- Target-mediated drug disposition (TMDD)
- Metabolite kinetics
- Drug-drug interaction (DDI) mechanisms
- Intracellular signaling or disease progression

**Steps:**
1. Export the PK-Sim® simulation to MoBi® (`.pkml` format).
2. Add new **Molecules**, **Reactions**, and/or **Spatial Structures** in MoBi® to represent the mechanistic extension.
3. Configure new **Passive Transports**, **Observers**, and **Initial Conditions** as needed.
4. Re-run the extended model and evaluate fit against additional datasets (e.g., PD endpoints, metabolite data).

**AI Assistance:**  
- An AI agent can propose the mathematical structure of mechanistic extensions (e.g., TMDD ODE system) and generate the corresponding MoBi® building block XML or R-based `{ospsuite}` modifications.
- An AI agent can identify relevant published models or MoBi® templates from the [OSP Model Repository](https://github.com/Open-Systems-Pharmacology) to serve as starting points.

**Acceptance Criteria:**  
- Extended model captures additional observed data endpoints.
- Model complexity is justified by the data (parsimony principle).

---

### Iteration 3 — Population Simulation and Variability Analysis

**Goal:** Scale the model to a virtual population and evaluate inter-individual variability.

**Steps:**
1. Create a **Population** building block in PK-Sim® defining the virtual population characteristics (age range, sex distribution, body weight, etc.).
2. Run a **population simulation** using PK-Sim® or `{ospsuite}` to generate individual PK profiles.
3. Compare population simulation percentiles (5th, 50th, 95th) against observed individual or mean±SD PK data.
4. Use `{ospsuite.bmlm}` for Bayesian parameter estimation to account for population-level variability.

**AI Assistance:**  
- An AI agent can generate R scripts for population simulation execution and result visualization using `{tlf}`.
- An AI agent can suggest population parameter adjustments based on comparison of predicted vs. observed variability.

**Acceptance Criteria:**  
- Predicted population PK variability is consistent with observed clinical PK variability.
- 50th percentile simulation aligns with mean observed PK profiles.

---

### Iteration 4 — Model Qualification and Reporting

**Goal:** Formally validate the model against an independent dataset and generate a qualification report.

**Steps:**
1. Apply the model to a held-out validation dataset (different dose, population, or study design).
2. Calculate predicted/observed (P/O) ratios for key PK metrics and assess whether they fall within acceptance criteria (typically 2-fold).
3. Use `{ospsuite.reportingengine}` to generate a standardized PBPK model evaluation report.
4. If the model is to be shared, create a qualification plan using the [OSP Qualification Framework](https://docs.open-systems-pharmacology.org/shared-tools-and-example-workflows/qualification).

**AI Assistance:**  
- An AI agent can generate the `{ospsuite.reportingengine}` workflow configuration.
- An AI agent can draft the written PBPK model report sections (model description, results, discussion).
- An AI agent can check that the qualification plan covers all required use cases.

**Acceptance Criteria:**  
- Predicted PK metrics for the validation dataset are within 2-fold of observed for ≥80% of data points (or as per regulatory guidance).
- Model evaluation report meets submission-ready standards.

---

## OSP Tools and Their Roles

### PK-Sim®

**Role:** Primary tool for initial whole-body PBPK model setup.

| Task | How PK-Sim® Is Used |
|---|---|
| Compound parameterization | Create Compound building block with physicochemical and in vitro data |
| Individual/Population creation | Define the virtual subject(s) for simulation |
| Enzyme/transporter expression | Add Expression Profiles from gene expression databases |
| Formulation | Define dosage form and dissolution characteristics |
| Protocol | Specify dose, route, and frequency |
| Simulation | Combine building blocks and run the PBPK simulation |
| CLI batch execution | Run multiple simulations non-interactively via [PK-Sim CLI](https://docs.open-systems-pharmacology.org/working-with-pk-sim/pk-sim-documentation/pk-sim-command-line-interface) |
| Snapshot export | Export project to JSON for version control and reproducibility |

**Documentation:** [PK-Sim® Documentation](https://docs.open-systems-pharmacology.org/working-with-pk-sim/pk-sim-documentation)

---

### MoBi®

**Role:** Expert-level tool for mechanistic model extensions and custom model structures.

| Task | How MoBi® Is Used |
|---|---|
| Model import from PK-Sim® | Import `.pkml` simulation for extension |
| Custom reactions and molecules | Add target binding, enzymatic reactions, metabolite kinetics |
| Modular model building | Use the [Modularization Concept](https://docs.open-systems-pharmacology.org/working-with-mobi/mobi-documentation/modularization-concept) to add/remove disease modules |
| SBML import | Import existing systems biology models |
| Observer customization | Define custom output quantities (e.g., tissue-to-plasma ratios) |
| Advanced parameterization | Implement complex kinetic expressions as formulas |

**Documentation:** [MoBi® Documentation](https://docs.open-systems-pharmacology.org/working-with-mobi/mobi-documentation)

---

### R Packages

| Package | Role in PBPK Workflow |
|---|---|
| **[`{ospsuite}`](https://www.open-systems-pharmacology.org/OSPSuite-R/)** | Load `.pkml` files, set parameters, run simulations, retrieve results, calculate PK parameters, sensitivity analysis |
| **[`{ospsuite.parameteridentification}`](https://www.open-systems-pharmacology.org/OSPSuite.ParameterIdentification/)** | Fit model parameters to observed PK data using optimization algorithms |
| **[`{ospsuite.globalsensitivity}`](https://github.com/Open-Systems-Pharmacology/OSPSuite.GlobalSensitivity)** | Global sensitivity analysis to rank influential parameters |
| **[`{ospsuite.bmlm}`](https://github.com/Open-Systems-Pharmacology/OSPSuite.BMLM)** | Bayesian multi-level modeling for population parameter estimation |
| **[`{ospsuite.reportingengine}`](https://www.open-systems-pharmacology.org/OSPSuite.ReportingEngine/)** | Automated generation of PBPK model evaluation reports |
| **[`{tlf}`](https://www.open-systems-pharmacology.org/TLF-Library/)** | Create standardized tables, listings, and figures for reports |
| **[`{ospsuite.utils}`](https://www.open-systems-pharmacology.org/OSPSuite.RUtils/)** | Common utility functions for OSP R workflows |

**Key Workflow Using `{ospsuite}` (R):**

```r
library(ospsuite)

# Load a simulation created in PK-Sim® or MoBi®
sim <- loadSimulation("MyDrug_PBPK.pkml")

# Set a drug parameter (e.g., lipophilicity)
setParameterValuesByPath(
  parameterPaths = "Organism|Drug|Lipophilicity",
  values = 2.5,
  simulation = sim
)

# Run the simulation
simResults <- runSimulations(simulations = sim)

# Get simulated plasma concentration-time data
outputPath <- "Organism|PeripheralVenousBlood|Drug|Plasma (Peripheral Venous Blood)"
results <- getOutputValues(simResults[[1]], quantitiesOrPaths = outputPath)

# Calculate PK parameters
pkAnalysis <- calculatePKAnalyses(results = simResults)
```

**Running Parameter Identification:**

```r
library(ospsuite)
library(ospsuite.parameteridentification)

# Define parameters to optimize
parameters <- list(
  PIParameter$new(
    path = "Organism|Liver|Drug|Intrinsic clearance",
    startValue = 10,
    minValue = 1,
    maxValue = 1000
  )
)

# Load observed data
observedData <- DataSet$new(name = "Clinical Study A")
observedData$addData(
  xValues = c(0.5, 1, 2, 4, 8, 12, 24),
  yValues = c(1.2, 2.5, 3.1, 2.8, 1.9, 1.2, 0.4),
  xUnit = "h",
  yUnit = "mg/l"
)

# Run parameter identification
piResult <- runParameterIdentification(
  simulation = sim,
  parameters = parameters,
  observedData = list(observedData)
)
```

---

### Qualification Framework

The [OSP Qualification Framework](https://docs.open-systems-pharmacology.org/shared-tools-and-example-workflows/qualification) enables automated, reproducible validation of PBPK models. It is used for:

- Releasing qualified OSP model libraries.
- Generating model qualification reports for regulatory submissions.
- Ensuring consistency across model updates.

---

## AI Tools and Integration

### Overview of AI/Agentic Roles

AI tools act as **orchestrators** and **accelerators** across the PBPK modeling workflow:

| AI Role | Description |
|---|---|
| **Data Extraction Agent** | Extract physicochemical, in vitro, and in vivo PK data from literature PDFs, databases, and regulatory documents |
| **Parameter Recommendation Agent** | Suggest initial parameter values based on drug class, structural similarity, or literature precedent |
| **Code Generation Agent** | Generate PK-Sim® CLI commands, R scripts, and MoBi® XML building blocks |
| **Simulation Orchestrator** | Manage iterative simulation-identification cycles, trigger tool calls programmatically |
| **Result Interpretation Agent** | Analyze goodness-of-fit plots, flag outliers, and propose next refinement steps |
| **Report Drafting Agent** | Draft PBPK model report sections from structured simulation outputs |

---

### Recommended AI Tools

#### 1. Large Language Models (LLMs) with Tool Use (e.g., GitHub Copilot, GPT-4o, Claude)

**How to use:**
- Provide the LLM with the OSP documentation context (e.g., by referencing [docs.open-systems-pharmacology.org](https://docs.open-systems-pharmacology.org/) and the [`{ospsuite}` R package documentation](https://www.open-systems-pharmacology.org/OSPSuite-R/)).
- Use the LLM to generate R scripts for simulation setup, parameter identification, and report creation.
- Have the LLM interpret sensitivity analysis and parameter identification results.

**Example prompt for code generation:**

> "Using the `{ospsuite}` R package, write a script that loads the file `MyDrug_IV.pkml`, sets lipophilicity to 2.5, runs the simulation, extracts plasma concentration-time data at peripheral venous blood, and calculates AUC and Cmax using `calculatePKAnalyses()`."

---

#### 2. AI Agents with Tool Calling (e.g., LangChain, AutoGen, GitHub Copilot Agents)

These frameworks allow AI agents to autonomously call tools (R functions, CLI commands, file I/O) in a feedback loop.

**Recommended agentic architecture:**

```
[User Input: Drug + PK Data]
        │
        ▼
[Data Extraction Agent]
  → Extract parameters from literature / databases
  → Populate OSP input templates
        │
        ▼
[Model Setup Agent]
  → Generate PK-Sim® CLI commands or ospsuite R script
  → Run initial bottom-up simulation
        │
        ▼
[Evaluation Agent]
  → Compare simulated vs. observed PK
  → Calculate P/O ratios and goodness-of-fit metrics
  → Decision: within criteria? → Report | else → Refinement
        │
        ▼
[Refinement Agent]
  → Run sensitivity analysis via {ospsuite}
  → Configure and run {ospsuite.parameteridentification}
  → Update simulation with optimized parameters
  → Return to Evaluation Agent
        │
        ▼
[Reporting Agent]
  → Configure {ospsuite.reportingengine}
  → Generate model evaluation report
  → Draft narrative sections
```

---

#### 3. Retrieval-Augmented Generation (RAG) for OSP Documentation

A RAG system can be set up to allow AI agents to query OSP documentation in real time:

- **Knowledge sources:**  
  - [OSP Documentation](https://docs.open-systems-pharmacology.org/) (full documentation site)
  - [`{ospsuite}` R package documentation](https://www.open-systems-pharmacology.org/OSPSuite-R/)
  - Published PBPK model repositories (e.g., [OSP Model Repository](https://github.com/Open-Systems-Pharmacology/))
  - Regulatory guidances (EMA, FDA PBPK guidelines)

- **Use cases:**  
  - Query: "What is the recommended calculation method for lipophilicity-based distribution in PK-Sim®?"
  - Query: "How do I configure the `{ospsuite.parameteridentification}` package for a two-stage fitting approach?"
  - Query: "Which transporter proteins are included in the PK-Sim® expression database for human small intestine?"

---

#### 4. AI-Assisted Literature Data Extraction

Tools like **Elicit**, **Semantic Scholar**, or custom LLM pipelines can extract PBPK-relevant parameters from published literature:

- Extract tables of physicochemical properties from drug monographs or synthesis papers.
- Parse clinical PK study results (n, dose, route, AUC, Cmax, t½, PK variability).
- Identify relevant in vitro metabolism and transport data.

**Output format for AI extraction agent:**  
The agent should output a structured JSON or CSV template compatible with PK-Sim® import formats:

```json
{
  "compound_name": "DrugX",
  "molecular_weight_g_mol": 385.4,
  "logP": 2.1,
  "pKa_acid": null,
  "pKa_base": 7.4,
  "fu_plasma": 0.12,
  "blood_plasma_ratio": 0.95,
  "Peff_cm_s": 2.3e-4,
  "CLint_uL_min_mg_protein": 45.2,
  "observed_pk": [
    {
      "study_id": "Study_A",
      "route": "IV",
      "dose_mg": 100,
      "population": "healthy adults",
      "timepoints_h": [0.25, 0.5, 1, 2, 4, 8, 12, 24],
      "concentration_ug_L": [850, 620, 440, 310, 195, 98, 51, 12]
    }
  ]
}
```

---

## End-to-End Agentic Workflow Example

The following illustrates how an AI-assisted agentic workflow might proceed for a new drug compound:

### Step 1: Input Collection (Human + AI)

- **Human provides:** Drug name, available study reports, and modeling goals.
- **AI Data Extraction Agent:** Mines literature and databases for physicochemical properties and in vitro ADME data. Outputs a structured parameter file.

### Step 2: Model Initialization (AI + PK-Sim®)

- **AI Model Setup Agent:** 
  - Translates the parameter file into a PK-Sim® snapshot (JSON) or calls the PK-Sim® CLI to create the compound, individual, and simulation building blocks.
  - Alternatively, generates an R script using `{ospsuite}` to create and configure the simulation programmatically.
- **PK-Sim®:** Executes the simulation and outputs results.

### Step 3: Initial Evaluation (AI + `{ospsuite}`)

- **AI Evaluation Agent:**
  - Loads simulation results via `{ospsuite}`.
  - Calculates P/O ratios for AUC and Cmax.
  - Generates goodness-of-fit plots using `{tlf}`.
  - Determines whether acceptance criteria are met.

### Step 4: Refinement Loop (AI + `{ospsuite.parameteridentification}`)

If acceptance criteria are not met:

- **AI Refinement Agent:**
  - Runs local sensitivity analysis to identify key parameters.
  - Configures `{ospsuite.parameteridentification}` with the identified parameters.
  - Executes parameter optimization.
  - Re-evaluates the model.
  - Repeats until criteria are met or until 3–5 refinement iterations have been performed.
- **Human review point:** After each major iteration, the modeling expert reviews the proposed parameter changes for scientific plausibility before accepting them.

### Step 5: Mechanistic Extension (Human + AI + MoBi®)

If simple parameter refinement is insufficient:

- **Human + AI Design Agent:** Discuss mechanistic hypotheses (e.g., saturable hepatic uptake). AI proposes the mathematical structure.
- **AI MoBi® Agent:** Generates the MoBi® building block XML or `{ospsuite}` R code for the mechanistic extension.
- **Human:** Reviews and approves the extension in MoBi®.

### Step 6: Population Simulation (AI + PK-Sim® / `{ospsuite}`)

- **AI Population Agent:** Configures a virtual population and runs population simulations.
- Results are compared to observed variability data.

### Step 7: Reporting (AI + `{ospsuite.reportingengine}`)

- **AI Reporting Agent:**
  - Configures and executes `{ospsuite.reportingengine}` to generate the PBPK model evaluation report.
  - Drafts written sections of the report (model description, parameter table, results, discussion).
- **Human:** Reviews, edits, and approves the final report.

---

## References

- [Open Systems Pharmacology Documentation](https://docs.open-systems-pharmacology.org/)
- [OSPSuite-R Documentation](https://www.open-systems-pharmacology.org/OSPSuite-R/)
- [PK-Sim® Documentation](https://docs.open-systems-pharmacology.org/working-with-pk-sim/pk-sim-documentation)
- [MoBi® Documentation](https://docs.open-systems-pharmacology.org/working-with-mobi/mobi-documentation)
- [OSP Qualification Framework](https://docs.open-systems-pharmacology.org/shared-tools-and-example-workflows/qualification)
- [OSP Model Repository](https://github.com/Open-Systems-Pharmacology)
- [`{ospsuite.parameteridentification}` Documentation](https://www.open-systems-pharmacology.org/OSPSuite.ParameterIdentification/)
- [`{ospsuite.reportingengine}` Documentation](https://www.open-systems-pharmacology.org/OSPSuite.ReportingEngine/)
- [EMA Guideline on the Reporting of PBPK Modelling and Simulation](https://www.ema.europa.eu/en/documents/scientific-guideline/guideline-reporting-physiologically-based-pharmacokinetic-pbpk-modelling-simulation_en.pdf)
- [FDA Guidance for Industry: Physiologically Based Pharmacokinetic Analyses](https://www.fda.gov/regulatory-information/search-fda-guidance-documents/physiologically-based-pharmacokinetic-analyses-format-and-content-guidance-industry)

---

All trademarks within this document belong to their legitimate owners.
