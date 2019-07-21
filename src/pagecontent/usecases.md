###PCD IG Home Page

### Current environment
Patients that move from one insurance carrier to another frequently lose coverage for existing care for chronic and acute conditions until the new payer is able to verify the clinical need for such treatments.  This frequently requires the payer to contact existing providers or have the patient see a new provider to establish documentation showing that the care is medically necessary and appropriate. The process creates a significant burden on providers and risk/cost to the patient.

### Background
The Healthcare industry in the US has recognized the need to support, where possible, continuity of care for both chronic and acute problems for individuals that move from one payer to another. The CMS NPRM on interoperability published in the Federal Register on March 3, 2019, requires that providers enable members to exchange clinical information from a prior payer to the current payer for a period of 5 years after they leave a plan and move to another covered plan. The exchange of information is intended, as indicated in the preamble to the NPRM, to enable continuity of care as a member moves from one covered plan to another without: 
1. forcing the member to pay for the care unless required as part of the change in plan coverage
2. have care terminated,  or
3. requiring providers to submit additional documentation to the new plan that was already available to the prior plan.

The goal is to provide for continuity of care and to minimise provider burden.  While the CMS NPRM points to the ability to exchange clinical data that is, at a minimum, defined by USCDI V1.0, the ability to exchange this data set does not provide the full set of information to enable the new payer to meet the goals stated above. 

### In Scope
This Implementation Guide (IG) is focused on organizing and exchanging information from a prior payer associated with current treatments to allow the new payer to continue these treatments without placing an additional burden on either the member or the provider.

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
6.	the supporting clinical documentation that justifies the need for those specific treatments.

All of the information may not be available in a structured format (or capable of being represented in the named FHIR resources).  However, the ability to indicate current problems, treatments, diagnoses, relevant guidelines, prior authorizations and associated clinical data as a coherent set of related information allows the new payer to review that information in light of the members need.  They can then, where possible, use it to support the continuation of treatment for chronic or acute illnesses without forcing the existing provider or a new provider to submit additional documentation that is duplicative of information that is already available to the prior payer.

### Clinical Requirements
Clinical requirements to support this implementation guide include the following:

1.  the ability to define current care that is being provided to the member which includes not just traditional treatments (e.g. home oxygen therapy for COPD. medications for management of diabetes) but also participation in disease management programst to support a member's chronic illnesses and help manage either specific clinical outcomes or quality of life;
2.  conditions that led to the need for these treatments, such as: COPD - to indicating a need for home oxygen therapy, diabetes - basis for medication management of diabetes, organ transplant - indicating the need for immunosuppressants, etc.;
3.  guidelines that are used to decide that such a treatment is appropriate.  These can include commercial guidelines such as MCG, InterQual and others.   The goal is to include a reference (e.g. URL) to the appropriate guideline;
4.	any specific prior authorizations that are in effect to support the treatment such as authorization for home oxygen therapy, and
5.	relevant clinical documentation that supports the need for the current therapy which may include, where appropriate, indications of prior therapies that have been tried and failed or have been considered and not pursued due to potential complications that are considered antecedent to the current therapy 

### Technical Requirements
### Authorization
The goal of this IG to supplement PDex and use the same of authorization process for member directed exchange. By using the SMART on FHIR implementation of OAuth 2.0 and providing an appropriate access token from a prior prayer plan to the new plan, the new plan's application can authenticate to the prior plan and the specific member's data.

Since the CMS NPRM requires that the member may direct the exchange of information from a prior plan for up to five years after a member leaves a plan, there may be the need to permit the current plan to access more than just the prior plan.  It may need to include a number of prior plans that fall within the 5 year time frame. 

#### Exchange structure and content
This IG uses a FHIR document approach, using the Composition resource to organize the information necessary to describe current treatment, conditions, guidelines and authorizations as well as current and historical clinical data necessary to support the decision to continue such treatment by the new payer.  

Where possible, information will be exchanged in a structured fashion using the HRex US Core profiles for FHIR R4 and/or other Da Vinci profiles.  Where information is available only in an unstructured format, the information will be exchanged in whatever format it is currently available using the DocumentReference resources to encapsulate the information in base64-encoded form.

#### Testing Requirements
This IG has been constructed in a manner that allows testing and validation - specifically:
* adherence to profiles defined in this guide
* adherence to profiles and communication patterns from referenced IGs such as PDex and HRex, and 
* testing of conformance to the underlying FHIR standards for the associated release (e.g. FHIR R4). 

####Workflow
The following is a description of the anticipated workflow defined by this IG:

Pre-conditions:
* A member of a covered plan has moved to another covered plan.
* That member is currently being treated for some chronic or acute condition and wishes the treatment to continue
* The new plan has an application registered with the prior plan

1. The member authorizes the prior plan to allow the new plan to access the member's clinical and treatment data and the prior plan provides an OAuth 2.0 token to the new plan.
2. The prior plan, in advance or on authorization, assembles the information defined in this IG as a FHIR document
3. The new plan accesses the prior plan via their API using the issued token and retrieves the FHIR document

    Note: if we are using either communicationrequest or subscription, expand on this section
4. The new plan receives the FHIR document and incorporates it into their UM/UR process for review and approval of the ongoing treatments for the new member.
 
The following diagram shows the workflow that is supported by this implementation guide:

Note: the legend for the workflow diagram includes swim lane for the member the new plan and the prior plan.  It indicates where authentication occurs, tokens are exchanged and information is requested buy an application in the new plan (or their contractor) by accessing the prior plants APIs using a token provided by the prior plan as part of the OAuth 2 .0 exchange(if necessary)

### Handling Sensitive Data
The prior plan SHALL provide a method for the member to verify if it is ok to exchange sensitive data as defined by federal, state and, where appropriate, local statue. In the event that the member does not authorise exchanges sensitive data, the prior plan SHALL have a method to sequester such information and make it unavailable for exchange. 

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
