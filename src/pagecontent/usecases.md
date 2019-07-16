###PCD IG Home Page

### Background

### Current environment
Currently patients that move from one insurance carrier to another frequently lose coverage for existing care for chronic and acute condition until the new payer is able to verify the clinical need for such treatments This frequently requires the payer to contact existing providers or have the patient see new provider to establish documentation showing that the care is medically necessary and appropriate. The process creates a significant burden on providers and risk/cost to the patient 

### Background
The Healthcare industry in the US has recognized the need to support, where possible, continuity of care for both chronic and acute problems for individuals that move from one payer to another. The CMS NPRM on interoperability published in the Federal Register on March 3, 2019, requires that providers enable members to exchange clinical information from a prior payer to the current payer for a period of 5 years after they leave a plan and move to another covered plan. The exchange of information is intended, as indicated in the preamble to the NPRM, to enable continuity of care as a member moves from one covered plan to another without: 1)  forcing the member to pay for the care unless required as part of the change in plan coverage 2) have care terminated,  or 3)  requiring providers to submit additional documentation to the new plan that was already available to the prior plan. The goal is to provide for continuity of care and to minimise provider burden.  While the CMS NPRM points to the ability to exchange clinical data that is, at a minimum, defined by USCDI V1.0 , the ability to exchange this data set does not provide the full set of information to enable the new payer to meet the goas stated above. 

### In Scope
This Implementation Guide (IG) Is focused on organizing and exchanging information from a prior payer associated with current treatments to allow the new player to continue these treatments without placing an additional burden on either the member or the provider 

###Out-of-Scope
This implementation guide is not:

1. intended to define the exchange of information from any entity other than a current or prior payer to a member directed entity. 
2. Prescriptive on what information is required to substantiate medical necessity for any treatment 
3. Expecting  the exchange of plan proprietary guidelines
4. intended to fully automate the review and authorization process for continuity of care for the new payer for current treatments r 

### Business Requirements
The goal of this implementation guide guide is complementary to the payer data exchange implementation guide (PDex) and is focused on providing a framework in which a payer can indicate: 

1.	current care that a member is receiving,
2.	conditions that led to the need for the current care ,
3.	guidelines that are used to evaluate the need for the care the patient is receiving,
4.	authorizations that are in effect,
5.	 success or failure of prior treatment that are relevant to the current treatments, as step therapies, and 
6.	the supporting clinical documentation that justifies the need for those specific treatment.

While all of the information may not be available in a structured format (or capable of being represented in the named FHIR resources), the ability to indicate current problems, treatments, diagnoses, relevant guidelines,  authorizations and associated clinical data as a coherent set of related information allows the new plan to review that information in light of the members need and, where possible, use it to support the continuation of treatment for chronic or acute illnesses without forcing the existing provider or a new provider to submit additional documentation that is duplicative of information that is already available to the prior payer.

### Clinical Requirements
Clinical requirements to support this implementation guide include the following:

1.  the ability to define current care that is being provided to the member which includes not just traditional treatments (e.g. home oxygen therapy for COPD. medications for management of diabetes) but also participation in disease management programs.
2.  conditions that led to the need for these treatments such as COPD to indicating a need for home oxygen therapy, diagnosis of diabetes of the basis for medication management of diabetes organ transplant indicating the need for immunosuppressants
3.  guidelines that are used to decide that such a treatment is appropriate --  these can include commercial guidelines such as MCG, InterQual and others
     
    The goal is to include a reference (e.g. URL) to the appropriate guideline
4.	any specific authorizations that are in effect to support the treatment such as authorization for home oxygen therapy, and
5.	relevant clinical documentation that supports the need for the current therapy which may include, where appropriate, indications of prior therapies that have been tried and failed or have been considered and not pursued due to potential complications that are considered antecedent to the current therapy 

### Technical Requirements
### Authorization
The goal of this IG to supplement PDex and use the same of authorization process for member directed exchange. By using the SMART of FHIR implementation of OAuth 2.0 and providing an appropriate access token from a prior prayer plan to the new plan, the new plan’s application can authenticate to the prior plan and the specific members data.
Since the CMS NPRM requires that the member may direct the exchange of information from a prior plan for up to five years after a member leaves a plan there may be the need to permit the current plan to access more than just the prior plan’ it may need to include a number of prior plans that fall within the 5 year time frame. 
Exchange structure and content
The goal of this IG is to use a FHIR document oriented approach, using the composition resource to organize the information necessary to describe current treatment, conditions, guidelines and authorizations as well as current and historical clinical data necessary to support the decision to continue such treatment by the new payer.  
Where possible information will be exchanged in a structured fashion using the HRex US Core profiles for FHIR R4.  Where information is available only in an unstructured format then the information will be exchange in whatever format it currently available by using the document reference resources to “contain” the information.
it is not the intent of this guide to provide an exchange standard that can automate the review and approval process by the new plan, rather to enable supporting information to be organised in a way that is understandable to the new plans utilization management process can easily access the information necessary to make the determinations that will allow for continuity of care and avoid placing a burden on providers to submit additional documentation when information currently exists and can be communicated from the prior plan 
Testing Requirements
It is the intent of this implementation guide to provide specifications for the exchange of payer coverage decisions in a way that is conducive to developing test scripts and a reference implementation (RI) that can be used to validate/exercise the IG  at connectathons and during piloting and production deployment. It is also the intent of this guide that any test scripts will include testing of: 1) resources and profiles defined in this guide, 2) artifacts use from referenced IGs such as PDex and HRex, and 3) testing of conformance to the underlying FHIR standards for the associated release (e.g. FHIR R4). 
Workflow
The following is a description of the anticipated workflow defined by this IG.

1.	A member of a covered plan has moved to another covered plan
2. The member is currently being treated for some chronic or acute condition and wishes the treatment to continue 
3.	The new plan has an application registered with the prior plan
4.	The member authorizes the prior plan to allow the new plan to access the member’s clinical and treatment data and the prior plan provides an OAuth 2.0 token to the new plan.
5.	The prior plan, in advance or on authorization, assembles the information defined in the is IG as a FHIR document
6.	The new plan accesses the prior plan via their API using the issued token and retrieves the FHIR document

    Note: if we are using either communicationrequest or subscription, expand on this section
7.	The new plan receives the FHIR document and incorporates it into their UM/UR process for review and approval of the ongoing treatments for the new member.
 
The following diagram shows the workflow that is supported by this implementation guide Note: the legend for the workflow diagram includes swim lane for the member the new plan and the prior plan.  It indicates where authentication occurs, tokens are exchanged and information is requested buy an application in the new plan (or their contractor) by accessing the prior plants APIs using a token provided by the prior plan as part of the OAuth 2 .0 exchange(if necessary)

### Handling Sensitive Data
The prior plan should provide a method for the member to verify if it is ok to exchange sensitive data as defined by federal, state and, where appropriate, local statue. In the event that the member does not authorised exchanges sensitive data the prior plan should have a method to sequester such information and make it unavailable for exchange. 

### Limitations of this Implementation Guide
The scope of this guide is intended to support the exchange of the basic information defined above which includes current patient treatment, associated conditions indicating the need for such treatment, reference to guidelines associated with the decision to provide coverage for such treatment, and the clinical information that was used to evaluate the need for and ultimately the approval of the coverage for the current treatment. It should be noted that included within the scope are not just traditional clinical treatments but also the ability to provide information related to other covered services that are currently being utilised by the member such as  weight management, disease management (e.g. diabetes) and other programs that may be in place to support a member's chronic illnesses and help manage either specific clinical outcomes or quality of life 
This guide may not be able to provide the exchange of structured information due to the lack of structured information available in the prayer information storage environment.  However, the IG it does provide guidance for the old payer to organise their information in a way that is consistent from payer to payer and provides for improvement in the ability of the new payer to review information related to existing treatment.  The information may, in many cases be sufficient to provide authorize continuity of treatment and coverage by the new payer without:  1) discontinuation of that treatment, 2) restart a treatment such a step therapy, and the need to go to the prior provider or a new provider to get documentation on the members need for the current treatments.  
 
### Use Cases
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
    3.	Number completed as of leaving the plan

### Da Vinci
Da Vinci is an HL7-sponsored project that brings together the U.S. payer ,providers, and technology suppliers (including EHR vendors)  to help payers and providers to positively impact clinical, quality, cost, and care management outcomes using FHIR-related technologies. The project organizes meetings (face-to-face and conference calls) as well as connectathons to find ways to leverage FHIR technologies to support and integrate value-based care (VBC) data exchange across communities. Da Vinci identifies value-based care use cases of interest to its member and the community as a whole.  The process that Da Vinci has adopted includes:

1. identify business, clinical, technical and testing requirements,
2. develop and ballot a FHIR based implementation guide (IG),
3.  develop a reference implementation (RI) that is used to demonstrate that the concepts in the IG are possible to implement,
4. pilot the standard
5. support the production use of the IG to enable exchange of data to support interoperability for value-based care.

Additional information about Da Vinci, its members, the use cases and the implementation guides being developed can all be found on the HL7 website. Meeting minutes and other materials can be found on the Da Vinci Confluence page.
