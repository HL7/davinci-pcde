### Current environment
Patients that move from one payer to another frequently experience interruptions or delays to existing care for chronic and acute conditions related to the inability of the new payer to obtain information about the ongoing treatment, understand its progress and verify the clinical need for such treatments.  This frequently requires the patient or providers to change therapies, tolerate delays in care, see additional providers or schedule additional visits, and fill out or resubmit additional documentation showing that the care is medically necessary and appropriate. The process creates a significant burden on providers, add unnecessary costs, and introduces risk to the patient.

### Background
The Healthcare industry in the US has recognized the need to support, where possible, continuity of care for both chronic and acute problems for individuals that move from one payer to another. The CMS NPRM on interoperability published in the Federal Register on March 3, 2019, requires that providers enable members to exchange clinical information from a prior payer to the current payer for a period of 5 years after they leave a plan and move to another covered plan. The exchange of information is intended, as indicated in the preamble to the NPRM, to enable continuity of care as a member moves from one covered plan to another without:
1. forcing the member to pay for the care unless required as part of the change in plan coverage
2. have care terminated,  or
3. requiring providers to submit additional documentation to the new plan that was already available to the prior plan.

The goal is to provide for continuity of care and to minimize provider burden.  While the CMS NPRM points to the ability to exchange clinical data that is, at a minimum, defined by USCDI V1.0, the ability to exchange this data set does not provide the full set of information to enable the new payer to meet the goals stated above.

### In Scope
This Implementation Guide (IG) is focused on organizing and exchanging information from a prior payer associated with current treatments to allow the new payer to continue these treatments without placing an additional burden on either the member or the provider or creating interruptions in care.

### Out-of-Scope
This implementation guide is not:

1. intended to define the exchange of information from any entity other than a current or prior payer to a member-directed entity;
2. prescriptive on what information is required to substantiate medical necessity for any treatment;
3. expecting the exchange of plan proprietary guidelines; or
4. intended to fully automate the review and authorization process for continuity of care for the new payer for current treatments

### Business Requirements
The goal of this implementation guide guide is complementary to the payer data exchange implementation guide (PDex) and is focused on providing a framework in which a payer can indicate:

1. current care that a member is receiving,
2. conditions that led to the need for the current care,
3. guidelines that were used to evaluate the need for the care the patient is receiving,
4. prior authorizations that are in effect,
5. successes or failures of prior treatment that are relevant to the current treatment, (e.g. step therapies), and
6. the supporting clinical documentation that justifies the need for those specific treatments.

All of the information may not be available in a structured format (or capable of being represented in the named FHIR resources).  However, the ability to indicate current problems, treatments, diagnoses, relevant guidelines, prior authorizations and associated clinical data as a coherent set of related information allows the new payer to review that information in light of the members need.  They can then, where possible, use it to support the continuation of treatment for chronic or acute illnesses without forcing the existing provider or a new provider to submit additional documentation that is duplicative of information that is already available to the prior payer.

### Clinical Requirements
Clinical requirements to support this implementation guide include the following:

1.  the ability to define current care that is being provided to the member which includes not just traditional treatments (e.g. home oxygen therapy for COPD. medications for management of diabetes) and their progress and the relevant providers of that care, but also participation in disease management programs to support a member's chronic illnesses and help manage either specific clinical outcomes or quality of life;
2.  conditions that led to the need for these treatments, such as: COPD - to indicating a need for home oxygen therapy, diabetes - basis for medication management of diabetes, organ transplant - indicating the need for immunosuppressants, etc.;
3.  guidelines that are used to decide that such a treatment is appropriate.  These can include commercial guidelines such as MCG, InterQual and others.   The goal is to include a reference (e.g. URL) to the appropriate guideline;
4.	any specific prior authorizations that are in effect to support the treatment such as authorization for home oxygen therapy, and
5.	relevant clinical documentation that supports the need for the current therapy which may include, where appropriate, indications of prior therapies that have been tried and failed or have been considered and not pursued due to potential complications that are considered antecedent to the current therapy

#### Testing Requirements
This IG has been constructed in a manner that allows testing and validation - specifically:
* adherence to profiles defined in this guide
* adherence to profiles and communication patterns from referenced IGs such as PDex, CDex and HRex, and
* testing of conformance to the underlying FHIR standards for the associated release (e.g. FHIR R4).

<!--### Use Cases
1. Home oxygen therapy for COPD
    1.	Treatment for chronic illness
    2.	Need diagnosis and possibly O2 Sat
2. Insulin treatment for Diabetes (step therapy)
    1.	Treatment for chronic illness
    2.	Need diagnosis and A1C
    3.	Need prior treatments that failed
3. Disease management program – for diabetes
    1.	Treatment for chronic illness
    2.	Foot care
    3.	Glaucoma
4. Home health physical therapy
    1.	Therapy treatment in the home
    2.	Number of sessions
    3.	Number completed as of leaving the plan -->
