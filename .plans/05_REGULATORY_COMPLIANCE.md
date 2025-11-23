# Regulatory & Compliance Strategy
## Closed-Loop Diabetes Management System

**Document Version:** 1.0
**Date:** 2025-11-23
**Status:** Draft

---

## Table of Contents
1. [Regulatory Overview](#1-regulatory-overview)
2. [FDA Pathway (United States)](#2-fda-pathway-united-states)
3. [International Regulations](#3-international-regulations)
4. [Quality Management System](#4-quality-management-system)
5. [Clinical Trial Strategy](#5-clinical-trial-strategy)
6. [Post-Market Surveillance](#6-post-market-surveillance)
7. [Data Privacy & HIPAA](#7-data-privacy--hipaa)
8. [Software as Medical Device (SaMD)](#8-software-as-medical-device-samd)
9. [Timeline & Milestones](#9-timeline--milestones)

---

## 1. Regulatory Overview

### 1.1 Device Classification

**United States (FDA):**
- **Classification:** Class III Medical Device
- **Regulation:** 21 CFR Part 862.1355 (Glucose monitoring) + 21 CFR Part 880.5725 (Insulin infusion pump)
- **Pathway:** Premarket Approval (PMA)
- **Reason:** Closed-loop insulin delivery is life-sustaining/supporting device

**European Union (MDR):**
- **Classification:** Class IIb (Rule 11 - Active therapeutic device)
- **Pathway:** Conformity Assessment with Notified Body
- **Regulation:** Medical Device Regulation (EU) 2017/745

**Canada (Health Canada):**
- **Classification:** Class III
- **Pathway:** Medical Device License Application

### 1.2 Regulatory Strategy

**Phased Approach:**

```
Phase 1: FDA Pre-Submission (Q-Sub)
  └─ Goal: Align on regulatory pathway and requirements
  └─ Duration: 3 months

Phase 2: Feasibility Study
  └─ Goal: Demonstrate proof of concept
  └─ Duration: 6-12 months

Phase 3: Pivotal Clinical Trial
  └─ Goal: Demonstrate safety and efficacy
  └─ Duration: 12-18 months

Phase 4: PMA Submission & Review
  └─ Goal: Regulatory approval
  └─ Duration: 12-18 months

Phase 5: Post-Market Surveillance
  └─ Goal: Ongoing safety monitoring
  └─ Duration: Continuous
```

---

## 2. FDA Pathway (United States)

### 2.1 Pre-Submission Meeting (Q-Sub)

**Purpose:**
- Discuss proposed clinical trial design
- Clarify regulatory requirements
- Obtain FDA feedback on endpoints and analysis

**Submission Package:**
1. Device description and intended use
2. Proposed clinical trial protocol
3. Risk analysis
4. Proposed primary/secondary endpoints
5. Statistical analysis plan
6. Comparison to predicate devices (e.g., Medtronic 780G, Tandem Control-IQ)

**Key Questions for FDA:**
- Is our clinical trial design adequate?
- Are our endpoints appropriate (TIR, hypoglycemia events)?
- What are the safety reporting requirements?
- Do we need a De Novo pathway or PMA?

### 2.2 IDE Application (Investigational Device Exemption)

**Required for Clinical Trials:**

**IDE Submission Contents:**
1. **Device Description**
   - Hardware components (CGM, pump)
   - Software architecture
   - Algorithm description
   - Risk analysis (ISO 14971)

2. **Pre-clinical Testing**
   - Bench testing results
   - Animal testing (if applicable)
   - Software validation
   - Cybersecurity assessment

3. **Clinical Protocol**
   - Study design (randomized controlled trial)
   - Inclusion/exclusion criteria
   - Sample size justification
   - Primary/secondary endpoints
   - Safety monitoring plan

4. **Informed Consent**
   - IRB-approved consent forms
   - Patient information materials

5. **Investigator Information**
   - Investigator CVs
   - Training materials
   - Site information

**Timeline:** 30 days for FDA review (or auto-approval if no response)

### 2.3 Clinical Trial Requirements

**Feasibility Study (Optional but Recommended):**
- **N:** 30-50 participants
- **Duration:** 3 months
- **Design:** Single-arm, open-label
- **Goal:** Demonstrate safety and preliminary efficacy

**Pivotal Trial (Required for PMA):**
- **N:** 200-300 participants
- **Duration:** 6 months
- **Design:** Randomized controlled trial (RCT)
  - **Arm 1:** Closed-loop system (intervention)
  - **Arm 2:** Sensor-augmented pump therapy (control)
- **Stratification:** Age groups, baseline HbA1c

**Primary Endpoint:**
- **Time in Range (TIR):** Percentage of time glucose is 70-180 mg/dL
- **Target:** Demonstrate superiority over control (>5% absolute improvement)

**Secondary Endpoints:**
- HbA1c change from baseline
- Time below range (<70 mg/dL)
- Time above range (>180 mg/dL)
- Severe hypoglycemic events
- Diabetic ketoacidosis (DKA) events
- User satisfaction (validated questionnaire)

**Safety Endpoints:**
- Serious adverse events (SAE)
- Device-related adverse events
- Adverse device effects (ADE)

### 2.4 PMA Submission

**PMA Package Contents:**

1. **Administrative Information**
   - Cover letter
   - Table of contents
   - Device description
   - Indications for use

2. **Non-Clinical Studies**
   - Bench testing
   - Software verification & validation
   - Biocompatibility (ISO 10993)
   - Electrical safety (IEC 60601-1)
   - Electromagnetic compatibility (EMC)
   - Cybersecurity documentation

3. **Clinical Studies**
   - Feasibility study results
   - Pivotal trial results
   - Statistical analysis
   - Clinical study reports

4. **Manufacturing Information**
   - Manufacturing process
   - Quality system documentation (ISO 13485)
   - Device master record
   - Labeling

5. **Risk Analysis**
   - FMEA (Failure Mode Effects Analysis)
   - Risk management report (ISO 14971)
   - Mitigations for identified risks

**FDA Review Timeline:**
- **Standard Review:** 180 days (6 months)
- **Interactive Review:** Ongoing Q&A with FDA
- **Panel Review:** Advisory panel meeting (likely for new technology)
- **Total Time:** 12-18 months typically

### 2.5 Post-Approval Requirements

**Post-Approval Study (PAS):**
- FDA may require additional data collection post-approval
- Typical duration: 2-5 years
- Monitor real-world safety and efficacy

**Annual Reports:**
- Submit annual report on device performance
- Report any MDR events (malfunctions, injuries, deaths)

**510(k) for Modifications:**
- Any significant changes require new regulatory submission

---

## 3. International Regulations

### 3.1 European Union (MDR)

**Medical Device Regulation (EU) 2017/745**

**Classification:** Class IIb

**Conformity Assessment:**
1. Engage Notified Body (e.g., TÜV SÜD, BSI)
2. Submit technical documentation
3. Notified Body audit and review
4. CE marking upon approval

**Technical Documentation:**
- Device description and specifications
- Risk management file (ISO 14971)
- Clinical evaluation report (CER)
- Post-market surveillance plan
- Labels and IFU (Instructions for Use)

**Clinical Evidence:**
- Clinical evaluation report (CER) required
- May accept FDA pivotal trial data
- May require EU-specific study

**UDI (Unique Device Identifier):**
- Required for traceability
- Register in EUDAMED database

**Timeline:** 12-18 months for CE marking

### 3.2 Canada (Health Canada)

**Classification:** Class III

**Pathway:** Medical Device License Application (MDLA)

**Requirements:**
- Pre-market review
- Quality system certification (ISO 13485)
- Clinical evidence (FDA data may be acceptable)
- Safety and effectiveness evidence

**Timeline:** 6-12 months

### 3.3 Other Markets

| Region | Regulatory Body | Requirements |
|--------|----------------|--------------|
| **UK** | MHRA | UKCA marking (similar to CE) |
| **Australia** | TGA | TGA registration (Class IIb) |
| **Japan** | PMDA | Shonin approval (challenging for new devices) |
| **Brazil** | ANVISA | ANVISA registration |
| **China** | NMPA | NMPA approval (requires local trials) |

---

## 4. Quality Management System

### 4.1 ISO 13485:2016 Compliance

**Quality Management System for Medical Devices**

**Key Requirements:**
1. **Management Responsibility**
   - Quality policy
   - Quality objectives
   - Management review

2. **Resource Management**
   - Training and competence
   - Infrastructure
   - Work environment

3. **Product Realization**
   - Design and development
   - Purchasing
   - Production and service provision
   - Control of monitoring and measuring devices

4. **Measurement, Analysis, Improvement**
   - Monitoring and measurement
   - Control of nonconforming product
   - Corrective and preventive actions (CAPA)
   - Internal audits

**Implementation Timeline:**
- **Month 1-3:** Gap analysis and planning
- **Month 4-9:** Implement processes and documentation
- **Month 10-12:** Internal audits and training
- **Month 13-15:** Certification audit (Stage 1 + Stage 2)

### 4.2 Design Controls (21 CFR Part 820.30)

**Design and Development Process:**

```
1. Design Planning
   - Design and development plan
   - Resource allocation
   - Risk management plan

2. Design Input
   - User needs
   - Regulatory requirements
   - Performance requirements
   - Safety requirements

3. Design Output
   - Design specifications
   - Software architecture
   - Algorithm documentation
   - Test specifications

4. Design Review
   - Formal reviews at key milestones
   - Cross-functional team
   - Document decisions

5. Design Verification
   - Does it meet design specifications?
   - Testing: unit, integration, system
   - Protocol and reports

6. Design Validation
   - Does it meet user needs?
   - Clinical validation
   - Usability testing

7. Design Transfer
   - Transfer to manufacturing
   - Process validation
   - Training

8. Design Changes
   - Change control process
   - Impact assessment
   - Re-verification/re-validation
```

### 4.3 Risk Management (ISO 14971)

**Risk Management Process:**

1. **Risk Analysis**
   - Identify hazards (FMEA)
   - Estimate risks (severity × probability)

2. **Risk Evaluation**
   - Compare to acceptability criteria
   - Benefit-risk analysis

3. **Risk Control**
   - Implement mitigations
   - Residual risk evaluation

4. **Post-Market Surveillance**
   - Monitor real-world risks
   - Update risk analysis

**Example Hazards:**
| Hazard | Severity | Probability | Risk Level | Mitigation |
|--------|----------|-------------|------------|------------|
| Insulin over-delivery | Catastrophic | Possible | High | Multiple safety limits, IOB tracking |
| Hypoglycemia prediction error | Critical | Probable | High | Conservative algorithms, user alerts |
| Device connectivity loss | Serious | Probable | Medium | Automatic fail-safe mode, alerts |
| Software bug | Serious | Possible | Medium | Rigorous testing, code reviews |
| Data breach | Moderate | Possible | Medium | Encryption, security audits |

---

## 5. Clinical Trial Strategy

### 5.1 Study Design

**Pivotal RCT Design:**

```
Study Title: Safety and Efficacy of DiabetesCare Loop Closed-Loop System in
             Adults with Type 1 Diabetes: A Randomized Controlled Trial

Design: Multicenter, randomized, controlled, parallel-group

Sample Size: 240 participants (120 per arm)
  - Power: 90%
  - Alpha: 0.05
  - Effect size: 5% difference in TIR
  - Dropout rate: 15%

Duration: 6 months

Inclusion Criteria:
  - Age 18-65 years
  - Type 1 diabetes for ≥1 year
  - HbA1c 7.0-10.0%
  - Currently using insulin pump + CGM
  - Willing to use study device

Exclusion Criteria:
  - Pregnancy or planning pregnancy
  - Severe hypoglycemia in past 6 months
  - DKA in past 6 months
  - Impaired awareness of hypoglycemia
  - eGFR <45 mL/min/1.73m²
  - Severe diabetic complications

Randomization:
  - 1:1 ratio
  - Stratified by: baseline HbA1c (<8.0% vs ≥8.0%), site
  - Centralized randomization system

Blinding: Open-label (not possible to blind closed-loop system)
```

### 5.2 Endpoints

**Primary Endpoint:**
- **Time in Range (70-180 mg/dL)** at 6 months
- **Analysis:** ANCOVA adjusting for baseline TIR

**Key Secondary Endpoints:**
1. HbA1c change from baseline
2. Mean glucose
3. Glucose variability (coefficient of variation)
4. Time below 70 mg/dL
5. Time below 54 mg/dL (Level 2 hypoglycemia)
6. Time above 180 mg/dL
7. Time above 250 mg/dL

**Safety Endpoints:**
1. Severe hypoglycemic events (requiring assistance)
2. DKA events
3. Serious adverse events (SAE)
4. Device-related adverse events
5. Adverse device effects

**User Experience Endpoints:**
1. Diabetes Distress Scale (DDS)
2. Diabetes Technology Questionnaire (DTQ)
3. Hypoglycemia Fear Survey (HFS)
4. System usability score (SUS)

### 5.3 Study Sites

**Target:** 10-15 sites in the US

**Site Selection Criteria:**
- Academic medical centers with diabetes expertise
- Experienced investigators (prior device trial experience)
- Strong recruitment potential
- IRB approval capability
- GCP-compliant

**Example Sites:**
- Joslin Diabetes Center (Boston)
- Barbara Davis Center (Denver)
- Stanford University
- UCSF
- Yale University

### 5.4 Data Safety Monitoring Board (DSMB)

**Composition:**
- Endocrinologist (Chair)
- Biostatistician
- Clinical trials expert
- Patient representative

**Responsibilities:**
- Review safety data quarterly
- Recommend trial modification/termination if needed
- Independent from sponsor

**Stopping Rules:**
- Severe hypoglycemia rate >2x control arm
- Any death attributable to device
- DKA rate >2x control arm

---

## 6. Post-Market Surveillance

### 6.1 Medical Device Reporting (MDR)

**21 CFR Part 803 - FDA MDR Requirements**

**Reportable Events:**
1. **Death:** Report within 30 days (and 5-day alert)
2. **Serious Injury:** Report within 30 days
3. **Malfunction (if could cause death/serious injury):** Report within 30 days

**Examples:**
- Severe hypoglycemia attributed to system
- Insulin over-delivery due to software bug
- Cybersecurity breach exposing patient data

**Process:**
```
Event Occurs
  ↓
User/Provider Reports to Company
  ↓
Company Evaluates (within 5 working days)
  ↓
Reportable? → Yes → File MDR Report (FDA Form 3500A)
            → No  → Document decision
  ↓
Root Cause Analysis
  ↓
Corrective Action (if needed)
  ↓
Update Risk Management File
```

### 6.2 Post-Market Surveillance Study

**Real-World Evidence Collection:**

**Study Design:**
- **Type:** Prospective, observational, single-arm
- **N:** 1,000+ users
- **Duration:** 3-5 years
- **Goal:** Monitor long-term safety and effectiveness

**Data Collection:**
- All glucose/insulin data (with consent)
- Adverse events
- Device malfunctions
- User satisfaction surveys
- Healthcare utilization

**Analysis:**
- Annual reports on TIR, HbA1c, hypoglycemia rates
- Compare to published benchmarks
- Identify subgroups with different outcomes

### 6.3 Vigilance and Field Actions

**Field Safety Corrective Action (FSCA):**

**When Required:**
- Safety issue identified post-market
- Device recall (Class I, II, or III)

**Process:**
1. Identify issue (e.g., software bug causing insulin miscalculation)
2. Assess risk and scope
3. Develop corrective action (e.g., software patch)
4. Notify regulators (FDA, Notified Body)
5. Notify customers
6. Implement fix
7. Verify effectiveness
8. Document and report

**Example Scenario:**
```
Issue: Software bug causes insulin pump to deliver 10% more insulin
       than commanded in rare edge case

Risk Assessment: Could cause hypoglycemia (Severity: High)

Action:
  1. Immediate: Alert users to manually verify insulin doses
  2. Short-term: Release software patch (within 2 weeks)
  3. Long-term: Enhanced testing to prevent similar bugs

Regulatory: File Field Safety Notice with FDA and EU regulators
```

---

## 7. Data Privacy & HIPAA

### 7.1 HIPAA Compliance (US)

**Health Insurance Portability and Accountability Act**

**Covered Entities:**
- Healthcare providers
- Health plans
- Healthcare clearinghouses

**Business Associates:**
- DiabetesCare Loop is a Business Associate (BA)
- Must sign Business Associate Agreement (BAA) with covered entities

**Protected Health Information (PHI):**
- Name, address, dates, phone, email
- Medical record number
- Device identifiers
- Glucose data, insulin data
- Any information that can identify patient

**Requirements:**

**Privacy Rule (45 CFR Part 164.500):**
- Minimum necessary principle
- Patient rights (access, amendment, accounting of disclosures)
- Privacy notice
- Consent for use/disclosure

**Security Rule (45 CFR Part 164.300):**
- Administrative safeguards (policies, training, access control)
- Physical safeguards (facility security, device controls)
- Technical safeguards (encryption, audit logs, authentication)

**Breach Notification Rule:**
- Notify individuals within 60 days of breach
- Notify HHS if >500 individuals affected
- Notify media if >500 in same state

### 7.2 GDPR Compliance (EU)

**General Data Protection Regulation (EU) 2016/679**

**Key Principles:**
1. **Lawfulness:** Legal basis for processing (consent, vital interests)
2. **Purpose Limitation:** Use data only for stated purpose
3. **Data Minimization:** Collect only what's necessary
4. **Accuracy:** Keep data accurate and up-to-date
5. **Storage Limitation:** Delete when no longer needed
6. **Integrity and Confidentiality:** Secure data

**Patient Rights:**
- Right to access data
- Right to rectification
- Right to erasure ("right to be forgotten")
- Right to data portability
- Right to object to processing

**Data Protection Impact Assessment (DPIA):**
- Required for high-risk processing (health data)
- Assess risks to patient privacy
- Document mitigations

**Data Processing Agreement (DPA):**
- Required with any sub-processors (cloud providers, etc.)

### 7.3 Data Retention

**Retention Policy:**

| Data Type | Retention Period | Rationale |
|-----------|------------------|-----------|
| **Glucose/Insulin Data** | 7 years | Regulatory requirement (21 CFR Part 11) |
| **Clinical Trial Data** | Lifetime of device + 10 years | FDA requirement |
| **Audit Logs** | 7 years | HIPAA requirement |
| **User Account Data** | Until user requests deletion | GDPR right to erasure |
| **De-identified Research Data** | Indefinitely | Not subject to HIPAA/GDPR |

---

## 8. Software as Medical Device (SaMD)

### 8.1 IEC 62304 Compliance

**Medical Device Software Lifecycle Processes**

**Software Safety Classification:**
- **Class C:** Software that could cause death or serious injury
  - Our closed-loop algorithm is Class C

**Lifecycle Processes:**
1. Software development planning
2. Software requirements analysis
3. Software architectural design
4. Software detailed design
5. Software unit implementation and verification
6. Software integration and integration testing
7. Software system testing
8. Software release

**Documentation Requirements:**
- Software development plan
- Software requirements specification
- Software design specification
- Software verification and validation plan
- Traceability matrix (requirements → tests)

### 8.2 Cybersecurity

**FDA Guidance: "Content of Premarket Submissions for Management of Cybersecurity in Medical Devices" (2023)**

**Threat Modeling:**
- Identify assets (patient data, insulin delivery commands)
- Identify threats (unauthorized access, malware, denial of service)
- Assess vulnerabilities
- Mitigate risks

**Security Controls:**
1. **Authentication:** Multi-factor authentication for users
2. **Authorization:** Role-based access control
3. **Encryption:** TLS 1.3, AES-256
4. **Logging:** Audit trails for all access
5. **Updates:** Secure software update mechanism
6. **Vulnerability Management:** Regular security assessments

**SBOM (Software Bill of Materials):**
- Document all software components and dependencies
- Track known vulnerabilities (CVEs)
- Update vulnerable components

### 8.3 Algorithm Transparency

**FDA Guidance on AI/ML-Based SaMD**

**Documentation:**
- Algorithm description (high-level, not source code)
- Training data description
- Performance metrics
- Limitations and assumptions
- Intended use population

**Algorithm Change Protocol (ACP):**
- Plan for how algorithm will be updated post-market
- SaMD Pre-Specifications (SPS): Define modification boundaries
- Algorithm Change Protocol (ACP): How changes will be managed

**Example:**
```
SPS: We may update glucose prediction model to improve accuracy
ACP: Updates will be validated on hold-out test set (RMSE <20 mg/dL)
     and deployed via canary release. FDA will be notified of
     significant changes (>10% performance improvement).
```

---

## 9. Timeline & Milestones

### 9.1 Regulatory Timeline (36-48 Months)

```
Month 0-3: Pre-Submission Preparation
  - Engage regulatory consultants
  - Prepare Q-Sub package
  - Submit to FDA

Month 3-6: Pre-Submission Meeting
  - FDA review (90 days)
  - Meeting with FDA
  - Incorporate feedback

Month 6-12: IDE Preparation and Submission
  - Finalize clinical protocol
  - Complete pre-clinical testing
  - IRB approvals
  - Submit IDE

Month 12-18: Feasibility Study
  - N=50 participants
  - 3-month follow-up
  - Data analysis
  - Prepare pivotal trial

Month 18-36: Pivotal Clinical Trial
  - N=240 participants
  - 6-month follow-up
  - Site enrollment: 6 months
  - Follow-up completion: 6 months
  - Data cleaning and analysis: 6 months

Month 36-40: PMA Preparation
  - Clinical study report
  - Statistical analysis
  - Compile PMA package
  - Submit to FDA

Month 40-52: PMA Review
  - FDA interactive review (180 days + extensions)
  - Respond to FDA questions
  - Advisory panel meeting (if needed)
  - Approval

Month 52+: Launch Preparation
  - Manufacturing scale-up
  - Marketing and education
  - Post-market surveillance setup
  - Commercial launch
```

### 9.2 Key Milestones

| Milestone | Target Month | Critical Path? |
|-----------|-------------|----------------|
| FDA Q-Sub meeting | Month 3 | Yes |
| IDE approval | Month 12 | Yes |
| ISO 13485 certification | Month 15 | Yes |
| Feasibility study results | Month 18 | Yes |
| Pivotal trial enrollment complete | Month 24 | Yes |
| Pivotal trial follow-up complete | Month 30 | Yes |
| PMA submission | Month 36 | Yes |
| FDA approval | Month 48 | Yes |
| CE mark | Month 36 | No (parallel) |
| Commercial launch | Month 50 | Yes |

---

## 10. Budget & Resources

### 10.1 Regulatory Budget Estimate

| Item | Cost | Notes |
|------|------|-------|
| **Regulatory Consultants** | $500K | FDA strategy, submissions |
| **Clinical Trials** | $5-8M | Sites, monitoring, data management |
| **Quality System (ISO 13485)** | $300K | Implementation, certification |
| **PMA User Fees** | $400K | FDA fee (2024 rate) |
| **Legal & IP** | $200K | Patents, agreements |
| **Post-Market Surveillance** | $500K/year | Ongoing monitoring |
| **International Approvals** | $500K | CE mark, Canada, etc. |
| **Total (to approval)** | **$8-10M** | |

### 10.2 Regulatory Team

**Internal Team:**
- VP of Regulatory Affairs
- Regulatory Affairs Specialist (FDA submissions)
- Quality Engineer (QMS, design controls)
- Clinical Affairs Manager (trials)
- Biostatistician

**External Partners:**
- Regulatory consulting firm
- Clinical research organization (CRO)
- Law firm (FDA law expertise)
- Notified body (EU approval)

---

## Appendix A: Regulatory References

**FDA Guidance Documents:**
1. "Artificial Pancreas Device Systems" (2018)
2. "Content of Premarket Submissions for Device Software Functions" (2023)
3. "Clinical Decision Support Software" (2022)
4. "Cybersecurity in Medical Devices" (2023)

**Standards:**
- ISO 13485:2016 - Quality Management Systems
- ISO 14971:2019 - Risk Management
- IEC 62304:2006 - Medical Device Software
- IEC 60601-1:2012 - Electrical Safety
- ISO 10993 - Biocompatibility

**Regulations:**
- 21 CFR Part 820 - Quality System Regulation
- 21 CFR Part 11 - Electronic Records
- 21 CFR Part 803 - Medical Device Reporting
- 45 CFR Part 164 - HIPAA Privacy and Security

---

**Document Approval:**
- Regulatory Affairs Lead: _________________
- Quality Assurance Lead: _________________
- Clinical Affairs Lead: _________________
- Legal Counsel: _________________

**Change Log:**
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2025-11-23 | Initial | First draft |
