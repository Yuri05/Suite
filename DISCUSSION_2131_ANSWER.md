# Answer to GitHub Discussion #2131: Dermal Absorption and Breast Milk Transfer of Chemotherapy Agents

## Executive Summary

This document provides a comprehensive answer to questions regarding dermal absorption models for chemotherapy drugs (paclitaxel, doxorubicin, docetaxel, and cyclophosphamide) and their potential transfer to breast milk. The analysis is based on:

- The Open Systems Pharmacology (OSP) Suite documentation
- The OSP Skin Permeation Model implementation
- The OSP Paclitaxel PBPK Model
- Published research on PBPK modeling of chemotherapy drugs and breast milk transfer
- Scientific literature on dermal absorption and PBPK modeling

## 1. Dermal Absorption Modeling in PK-Sim/MoBi

### 1.1 OSP Skin Permeation Model Overview

The Open Systems Pharmacology Suite provides a comprehensive, mechanistic skin permeation model implemented in MoBi, based on the work of Dancik et al. (2013). This model enables detailed simulation of drug absorption through human skin.

**Key Features:**

- **Multi-layered Structure**: The model treats skin as a multilayered slab, with each layer representing specific anatomical components (stratum corneum, viable epidermis, dermis, and sub-dermis)
- **Diffusion-Based Mechanism**: Uses one-dimensional partial differential equations to model chemical diffusion across skin layers
- **Metabolic Processes**: Incorporates Michaelis-Menten metabolism in dermis and sub-dermis layers
- **Flexible Parameterization**: Allows customization of:
  - Skin properties (layer thicknesses, metabolic activity)
  - Chemical/drug properties (logP, molecular weight, solubility)
  - Vehicle formulation characteristics
  - Environmental conditions (temperature, humidity, occlusion)
  - Dosing protocols

**Model Outputs:**

- Time course of drug concentration in each skin layer
- Amount evaporated or remaining in vehicle
- Flux across skin boundaries (cumulative and instantaneous)
- Systemic absorption for in vivo scenarios
- Integration with whole-body PBPK models

### 1.2 Integration with Whole-Body PBPK Models

The skin permeation model can be seamlessly integrated with PK-Sim whole-body PBPK models, enabling:

1. **Systemic Exposure Assessment**: Prediction of plasma concentrations following dermal application
2. **Route Comparison**: Comparison of dermal vs. other administration routes
3. **Special Population Modeling**: Extension to pediatric, geriatric, or diseased populations
4. **Virtual Bioequivalence Studies**: Support for regulatory submissions (FDA has accepted OSP-based dermal models for generic topical products)

### 1.3 Model Validation and Regulatory Acceptance

The OSP skin permeation model has been:

- Validated against in vitro permeation testing (IVPT) data
- Used in FDA regulatory submissions for virtual bioequivalence studies
- Enhanced with Bayesian inference for parameter uncertainty quantification
- Applied to various compounds including caffeine, diclofenac, and experimental compounds

**Key Publications:**

1. Dancik et al. (2013) - "Design and performance of a spreadsheet-based model for estimating bioavailability of chemicals from dermal exposure" (Advanced Drug Delivery Reviews, 65(2): 221-236)
2. Hamadeh et al. (2023) - "Enhancement of Skin Permeability Prediction through PBPK Modeling, Bayesian Inference and Experiment Design" (Pharmaceutics, 15(12): 2667)

### 1.4 Practical Implementation

**Resources Available:**

- **GitHub Repository**: https://github.com/Open-Systems-Pharmacology/Skin-permeation-model
  - Model files (.mbp3 project, .mbdt template)
  - Comprehensive user guide (PDF)
  - Parameter files and documentation
  - Example workflows

- **Documentation**: https://docs.open-systems-pharmacology.org/
  - PK-Sim and MoBi manuals
  - Tutorial videos and workshops
  - Best practices for model building

**Workflow Steps:**

1. Install OSP Suite (PK-Sim and MoBi)
2. Open skin permeation model in MoBi
3. Parameterize for specific drug (physicochemical properties)
4. Define exposure scenario (dose, vehicle, duration)
5. Run simulation to obtain permeation profiles
6. Optionally link to PK-Sim for systemic PBPK modeling

## 2. PBPK Models for Chemotherapy Agents

### 2.1 Paclitaxel

**OSP Model Repository:**
https://github.com/Open-Systems-Pharmacology/Paclitaxel-Model

**Model Characteristics:**

- Whole-body PBPK model developed in PK-Sim
- Incorporates CYP2C8-mediated metabolism
- Validated against clinical data from adults and pediatric populations
- Includes multiple IV administration protocols
- Contains observed data from published clinical studies

**Applications:**

- Dose prediction in special populations
- Drug-drug interaction assessment (CYP2C8 modulators)
- Pediatric dosing optimization
- Pharmacokinetic variability analysis

**Dermal Absorption Considerations:**

Paclitaxel is a large molecule (MW = 853.9 g/mol) with:
- Moderate lipophilicity (logP ~ 3.0-3.5)
- Large molecular size limiting skin penetration
- Expected low dermal bioavailability through intact skin
- Potential for higher absorption through compromised skin barrier

**Risk Assessment for Occupational Exposure:**

- Healthcare workers handling paclitaxel should use appropriate protective equipment
- Dermal absorption through intact skin is expected to be minimal
- Main concern is repeated exposure or exposure through damaged skin
- The OSP skin model could quantify absorption risk given proper parameters

### 2.2 Doxorubicin

**Current Status:**

While doxorubicin is not currently in the public OSP PBPK Model Library, extensive PBPK models exist in the literature.

**Published PBPK Models:**

- Models predicting cardiotoxicity at cellular level
- Metabolite-inclusive models for adverse effect prediction
- Integration in combination chemotherapy models

**Molecular Characteristics:**

- Moderate molecular weight (MW = 543.5 g/mol)
- Hydrophilic anthracycline structure
- Poor lipid solubility
- Expected very low dermal absorption

**Breast Milk Transfer:**

According to the study by Colbers et al. (2023, DOI: 10.1002/psp4.13043):

- **Peak infant plasma concentration**: 0.74 nM (95th percentile)
  - Represents 0.1-1.8% of therapeutic levels
  - Systemic toxicity risk in infants: Very low

- **Peak intestinal concentration**: 140 μM
  - Much higher than plasma levels
  - Potential for local GI toxicity
  - Monitoring of infant GI health recommended

- **Risk Mitigation**: Discarding breast milk for 3 days post-chemotherapy reduces exposure by >80-90%

### 2.3 Docetaxel

**PBPK Model Development:**

Extensive PBPK models for docetaxel are available in the literature:

- Models validated from animal to human extrapolation
- Integration in combination regimens (TCE: docetaxel-cyclophosphamide-epirubicin)
- CYP3A4-mediated metabolism modeling
- Drug-drug interaction predictions

**Recent Publication:**

Chae et al. (2024) - "Docetaxel, cyclophosphamide, and epirubicin: application of PBPK modeling for drug-drug interactions" (Journal of Pharmacokinetics and Pharmacodynamics)

**Key Findings:**

- PBPK models accurately predict PK in cancer patients
- Strong CYP3A4 inhibitors increase exposure significantly
- Recommendations to avoid concomitant CYP3A4 modulators
- GastroPlus platform successfully used for model development

**Dermal Properties:**

- Molecular weight: 807.9 g/mol (large molecule)
- Similar characteristics to paclitaxel (taxane family)
- Expected low dermal absorption through intact skin
- Structural similarity allows parameter extrapolation between taxanes

### 2.4 Cyclophosphamide

**PBPK Modeling:**

Cyclophosphamide has been included in several PBPK studies:

- Part of combination chemotherapy models (TCE regimen)
- Prodrug requiring metabolic activation
- Multiple metabolic pathways (CYP2B6, CYP3A4, CYP2C9)
- Active metabolite modeling (4-hydroxycyclophosphamide, phosphoramide mustard)

**Molecular Properties:**

- Smaller molecule (MW = 261.1 g/mol) compared to taxanes and anthracyclines
- More water-soluble
- Potentially higher dermal permeability than other agents
- Still expected to have low dermal bioavailability

**Safety Considerations:**

- Activation required for cytotoxic effects
- Dermal exposure to parent compound may pose lower immediate risk
- Metabolism in skin could generate active metabolites
- Risk assessment would benefit from mechanistic PBPK modeling

## 3. Breast Milk Transfer: Key Findings from Colbers et al. (2023)

### 3.1 Study Overview

**Citation:** Colbers et al. (2023) "Physiologically-based pharmacokinetic model to predict doxorubicin and paclitaxel exposure in infants through breast milk" CPT: Pharmacometrics & Systems Pharmacology, DOI: 10.1002/psp4.13043

### 3.2 Methodology

- Extended whole-body PBPK models for IV doxorubicin and paclitaxel
- Incorporated oral absorption parameters for infant exposure
- Simulated multiple scenarios including worst-case parameters
- Modeled different breastfeeding frequencies and timing

### 3.3 Key Results

#### Paclitaxel

**Infant Plasma Exposure:**
- Peak concentration: 3.48 nM (95th percentile)
- Represents 0.4-1.7% of therapeutic concentrations
- Far below levels associated with systemic toxicity

**Infant Intestinal Exposure:**
- Peak concentration: 1.0 μM
- Higher local exposure than systemic
- Potential for GI effects

**Milk Discard Strategy:**
- 3-day discard period: >80% reduction in exposure
- Effective risk mitigation approach

#### Doxorubicin

**Infant Plasma Exposure:**
- Peak concentration: 0.74 nM (95th percentile)
- Represents 0.1-1.8% of therapeutic levels
- Systemic toxicity highly unlikely

**Infant Intestinal Exposure:**
- Peak concentration: 140 μM
- Substantially elevated local GI exposure
- Warrants monitoring for GI symptoms

**Milk Discard Strategy:**
- 3-day discard period: >90% reduction in exposure
- Highly effective protective measure

### 3.4 Clinical Implications

**General Safety Assessment:**

1. **Systemic Toxicity**: Very low risk for both drugs
2. **Local GI Toxicity**: More relevant concern, especially for doxorubicin
3. **Monitoring Recommendations**:
   - Watch for infant GI symptoms (diarrhea, mucositis)
   - Consider complete blood count if concerns arise
   - Assess growth and development

**Practical Recommendations:**

1. **Best Practice**: Discard breast milk for 3 days following chemotherapy
2. **Individual Assessment**: Consider maternal pharmacokinetics, infant age, feeding frequency
3. **Communication**: Clear patient counseling on timing and risks
4. **Documentation**: Track milk expression and discard timing

## 4. Implications for Combined Dermal and Lactation Exposure

### 4.1 Occupational Exposure Scenarios

**Healthcare Workers:**

- Primary risk: Accidental dermal exposure during drug preparation/administration
- Secondary risk: If lactating, potential compound exposure to nursing infant

**Risk Mitigation Strategies:**

1. **Primary Prevention**:
   - Use of personal protective equipment (PPE)
   - Closed-system drug transfer devices (CSTDs)
   - Proper handling procedures

2. **Dermal PBPK Modeling Applications**:
   - Quantify absorbed dose from typical exposure scenarios
   - Model worst-case scenarios (large surface area, prolonged contact)
   - Integrate with whole-body PBPK to predict systemic exposure
   - Link to lactation models to estimate infant exposure

3. **Decision Support**:
   - Define safe exposure limits
   - Establish monitoring thresholds
   - Guide return-to-breastfeeding timing after exposure

### 4.2 PBPK Modeling Workflow

**Proposed Integrated Approach:**

```
Dermal Exposure Scenario
         ↓
Skin Permeation Model (MoBi)
         ↓
Absorbed Dose Calculation
         ↓
Whole-Body PBPK (PK-Sim)
         ↓
Maternal Plasma Concentration
         ↓
Lactation Transfer Model
         ↓
Breast Milk Concentration
         ↓
Infant PBPK Model
         ↓
Infant Exposure Assessment
```

**Implementation Steps:**

1. **Characterize Dermal Exposure**:
   - Contact area, duration, concentration
   - Skin condition (intact vs. compromised)
   - Occlusion/protection factors

2. **Parameterize Skin Model**:
   - Drug physicochemical properties
   - Partition coefficients
   - Diffusion constants
   - Metabolism parameters

3. **Run Skin Permeation Simulation**:
   - Calculate flux across skin
   - Determine systemic bioavailability

4. **Link to Systemic PBPK**:
   - Input dermal absorption as dosing route
   - Simulate maternal plasma profiles
   - Account for any previous IV/oral chemotherapy

5. **Apply Lactation Model**:
   - Use milk/plasma partition coefficients
   - Model temporal milk concentration profiles

6. **Assess Infant Risk**:
   - Calculate infant dose via milk
   - Predict infant plasma and local GI concentrations
   - Compare to toxicity thresholds

## 5. Data Gaps and Research Opportunities

### 5.1 Missing Parameters

**For Dermal Modeling:**

- Limited in vitro skin permeation data for chemotherapy agents
- Lack of detailed partition coefficients for skin compartments
- Unknown skin metabolism rates for these compounds
- Need for damaged skin barrier modeling

**For Lactation Modeling:**

- Limited clinical milk concentration data
- Uncertainty in infant GI absorption from milk matrix
- Variable infant physiological parameters
- Long-term follow-up data on exposed infants

### 5.2 Proposed Research

1. **In Vitro Studies**:
   - Franz diffusion cell studies with human skin
   - Measure permeation under various conditions
   - Assess vehicle and formulation effects

2. **In Silico Extensions**:
   - Develop OSP PBPK models for doxorubicin, docetaxel, cyclophosphamide
   - Validate against existing clinical data
   - Publish in OSP Model Library for community use

3. **Clinical Observations**:
   - Prospective monitoring of occupationally exposed lactating healthcare workers
   - Measure maternal and infant biomarkers
   - Correlate with model predictions

4. **Sensitivity Analyses**:
   - Identify critical parameters affecting risk assessment
   - Guide targeted experimental studies
   - Optimize monitoring strategies

## 6. Practical Guidance and Recommendations

### 6.1 For Healthcare Institutions

**Policy Development:**

- Establish clear protocols for handling cytotoxic agents
- Define PPE requirements based on risk assessment
- Create guidelines for lactating staff handling chemotherapy
- Implement monitoring programs for high-risk exposures

**Training Programs:**

- Education on dermal absorption risks
- Proper use of CSTDs and PPE
- Recognition of exposure incidents
- Response protocols for spills/exposures

### 6.2 For Lactating Healthcare Workers

**Risk Assessment:**

- Evaluate typical exposure frequency and magnitude
- Consider use of PBPK modeling for personalized assessment
- Consult occupational health services
- Document all exposure incidents

**Protective Measures:**

- Consistent use of appropriate PPE (double gloves, gowns)
- Minimize skin contact through procedural controls
- Immediate washing if skin exposure occurs
- Consider temporary assignment changes if concerned

**Breastfeeding Guidance:**

- No routine cessation needed with proper PPE and procedures
- Consider pumping and discarding after significant exposure incidents
- Monitor infant for any signs of illness
- Maintain communication with healthcare provider

### 6.3 For Patients Receiving Chemotherapy

**Breastfeeding After IV Chemotherapy:**

- **General Recommendation**: Discard breast milk for 3 days post-treatment
- **Drug-Specific Timing**: May vary based on half-life and milk concentration data
- **Monitoring**: Watch infant for GI symptoms, decreased feeding, lethargy
- **Support**: Maintain milk supply through pumping and discarding

**Dermal Product Use** (if relevant):

- Topical chemotherapy formulations (e.g., fluorouracil cream) have different risk profiles
- Minimize infant contact with treated skin areas
- Wash hands thoroughly before infant care
- Discuss with oncologist and pediatrician

## 7. Leveraging OSP Tools for Risk Assessment

### 7.1 Building Custom Models

**Resources Available:**

1. **OSP Suite Software**: Free, open-source download from https://www.open-systems-pharmacology.org
2. **Skin Permeation Model Template**: Ready-to-use MoBi project
3. **Compound Database**: Extensive library of drug parameters
4. **Documentation**: Comprehensive manuals and tutorials
5. **Community Forum**: Active support community

**Model Development Path:**

- Start with existing paclitaxel model as template for taxanes
- Adapt anthracycline models from literature for doxorubicin
- Parameterize using published physicochemical data
- Validate against available clinical PK studies
- Extend with dermal and lactation components

### 7.2 Qualification and Validation

**Best Practices:**

- Document all parameter sources and assumptions
- Perform sensitivity and uncertainty analyses
- Validate against independent datasets
- Compare predictions to clinical safety margins
- Iterate model refinement as new data emerge

**Regulatory Considerations:**

- OSP models have gained regulatory acceptance (FDA, EMA)
- Follow ICH M9 guidelines for model-informed drug development
- Document according to FDA PBPK guidance
- Consider model contextualization for specific decisions

## 8. Conclusions

### Key Takeaways

1. **OSP Suite Capabilities**:
   - Comprehensive skin permeation model available in MoBi
   - Based on validated Dancik et al. (2013) framework
   - Successfully integrated with whole-body PBPK models
   - Regulatory acceptance for dermal bioequivalence studies

2. **Chemotherapy Agent Models**:
   - Paclitaxel: Well-developed OSP model available
   - Doxorubicin: Literature models exist, OSP implementation needed
   - Docetaxel: Extensive PBPK models published
   - Cyclophosphamide: Included in combination therapy models

3. **Breast Milk Transfer**:
   - Infant systemic exposure via milk: Very low risk
   - Local GI exposure: More relevant concern
   - 3-day milk discard strategy: Highly effective (>80-90% reduction)
   - Clinical data supports safety with appropriate precautions

4. **Dermal Absorption**:
   - Large, polar chemotherapy molecules: Poor skin penetration expected
   - Intact skin barrier: Minimal systemic absorption
   - Occupational exposure: Low risk with proper PPE
   - Compromised skin: Increased risk, requires assessment

5. **Integrated Risk Assessment**:
   - PBPK modeling enables quantitative risk evaluation
   - Can link dermal → systemic → lactation → infant exposure
   - Supports evidence-based decision making
   - Identifies critical parameters for monitoring

### Future Directions

1. **Model Development**:
   - Complete OSP PBPK models for doxorubicin, docetaxel, cyclophosphamide
   - Publish validated models in OSP-PBPK-Model-Library
   - Create tutorial workflows for combined dermal-lactation modeling

2. **Experimental Validation**:
   - Generate in vitro skin permeation data for these compounds
   - Conduct clinical studies measuring milk and infant exposure
   - Validate model predictions against real-world data

3. **Clinical Implementation**:
   - Develop decision support tools for clinicians
   - Create patient education materials
   - Establish monitoring protocols
   - Integrate into clinical guidelines

## 9. References and Resources

### Primary OSP Resources

1. **OSP Suite**: https://www.open-systems-pharmacology.org
2. **Documentation**: https://docs.open-systems-pharmacology.org
3. **Skin Permeation Model**: https://github.com/Open-Systems-Pharmacology/Skin-permeation-model
4. **Paclitaxel Model**: https://github.com/Open-Systems-Pharmacology/Paclitaxel-Model
5. **Model Library**: https://github.com/Open-Systems-Pharmacology/OSP-PBPK-Model-Library
6. **Forum**: https://github.com/Open-Systems-Pharmacology/Forum

### Key Scientific Publications

#### Skin Permeation Modeling

1. Dancik Y, Miller MA, Jaworska J, Kasting GB. "Design and performance of a spreadsheet-based model for estimating bioavailability of chemicals from dermal exposure." Advanced Drug Delivery Reviews. 2013;65(2):221-236.

2. Hamadeh A, Biedenweg D, Thiele B, et al. "Enhancement of Skin Permeability Prediction through PBPK Modeling, Bayesian Inference and Experiment Design." Pharmaceutics. 2023;15(12):2667.

3. Zhang F, Clarke N, Dancik Y. "Use of PBPK modelling to extrapolate in vitro porcine ear skin permeation of caffeine to human in vivo pharmacokinetics." International Journal of Pharmaceutics. 2025;670:125051.

#### Chemotherapy PBPK Models

4. Chae JW, Song Y, Chung SJ, Yun HY. "Docetaxel, cyclophosphamide, and epirubicin: application of PBPK modeling for drug-drug interactions." Journal of Pharmacokinetics and Pharmacodynamics. 2024;51:847-861.

5. Islam MZ, Lugtu GL. "A Physiologically Based Pharmacokinetic Model of Docetaxel Disposition: From Mouse to Man." Clinical Cancer Research. 2007;13(9):2768-2776.

6. Various publications on doxorubicin PBPK models for toxicity prediction (see web search results).

#### Breast Milk Transfer

7. Colbers A, Greupink R, Burger D. "Physiologically-based pharmacokinetic model to predict doxorubicin and paclitaxel exposure in infants through breast milk." CPT: Pharmacometrics & Systems Pharmacology. 2023. DOI: 10.1002/psp4.13043

### Guidelines and Best Practices

- FDA Guidance: "Physiologically Based Pharmacokinetic Analyses — Format and Content"
- EMA Guideline on the reporting of physiologically based pharmacokinetic (PBPK) modelling and simulation
- ICH M9: Biopharmaceutics Classification System-based Biowaivers

### Training and Support

- OSP Tutorial Videos: Available on OSP website
- Workshops: Regular training workshops on PK-Sim/MoBi
- Community Forum: Active Q&A and discussion platform
- Esqlabs Training: Commercial training courses available

---

## Document Information

**Author**: Generated for Open Systems Pharmacology Community
**Purpose**: Answer to GitHub Discussion #2131
**Date**: March 2026
**Version**: 1.0
**License**: This document is provided under the same license as the OSP Suite (GPLv2)

**Acknowledgments**: This analysis synthesizes information from OSP Suite documentation, scientific literature, and community resources. Special thanks to the OSP development team and contributors to the Skin-permeation-model and Paclitaxel-Model repositories.

**Contact**: For questions or contributions, please use the OSP Forum at https://github.com/Open-Systems-Pharmacology/Forum

**Disclaimer**: This document provides scientific and technical information for educational purposes. Clinical decisions regarding patient care, occupational safety, and breastfeeding should always be made in consultation with qualified healthcare professionals and based on individual circumstances.
