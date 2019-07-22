This page is broken into three sections which outline the expectations for systems wishing to comply with the IG:
* General [context](#context) that implementers need to understand in reading the remainder of the guide

* A description of the technical [data exchange](#data-exchange) mechanisms by which a patient requests and enables that information to be transmitted from the original payer to the new payer and the means by which that transfer actually occurs.

* A description of the [data structure](#data-structure) to be shared that supports a transition of coverage responsibility from one payer plan to another

### Context
#### Pre-reading
Before reading this formal specification, implementers should first familiarize themselves with two other key portions of the specification:

* The [Use Cases & Overview](usecases.html) page provides context for what this formal specification is trying to accomplish and will give a sense of both the business context and general process flow enabled by the formal specification below.

* The [Technical Background](background.html) page provides information about the underlying specifications and indicates what portions of them should be read and understood to have necessary foundation to understand the constraints and usage guidance described here.


#### Conventions
This implementation guide uses specific terminology to flag statements that have relevance for the evaluation of conformance with the guide:

* **SHALL** indicates requirements that must be met to be conformant with the specification.

* **SHOULD** indicates behaviors that are strongly recommended (and which may result in interoperability issues or sub-optimal behavior if not adhered to), but which do not, for this version of the specification, affect the determination of specification conformance.

* **MAY** describes optional behaviors that are free to consider but where the is no recommendation for or against adoption.


#### Profiles
This specification makes significant use of [FHIR profiles]({{site.data.fhir.path}}profiling.html) and terminology artifacts to describe the content to be shared as part of prior authorization requests and responses.

The full set of profiles defined in or used by this implementation guide can be found by following the links on the [Artifacts](allartifacts.html) page.

#### Terminology
This IG defines custom codes for document types and sections.  These codes should be considered 'temporary'.  After implementation testing and confirmation, these custom codes will migrate to standardized codes in an official code system - most likely LOINC.

### Data Exchange
This data exchange builds on the Da Vinci [Payer Data Exhange (PDex)](http://hl7.org/fhir/us/davinci-pdex/2019Jun) and [Clinical Data Exhange (CDex)](http://hl7.org/fhir/us/davinci-pdex/2019Jun) implementation guides, leveraging the [OAuth 2.0-based](http://hl7.org/fhir/us/davinci-pdex/2019Jun/3-4_Interaction_Methods.html#3-4-2-oauth20-and-fhir-api) mechanism to enable data flow between two payer systems and the [non-Task Solicited Communication](http://hl7.org/fhir/us/davinci-cdex/2019Jun/Request_(Solicited_Communication).html#solicited-communication-without-task) mechanism to request the desired document.  This section of the implementation guide provides details on that flow.


#### Pre-conditions
For this implementation guide to be applicable, the following conditions must be met:

* A member of a covered plan has moved to another covered plan.

* That member is currently being treated for some chronic or acute condition and wishes the treatment to continue

* The system/portal for the new plan knows the base FHIR API URL for (and has basic business arrangements in place allowing query of data from) the original payer


#### Workflow
1. The member uses an interface/portal or SMART app within the new plan to authenticate to the old plan and authorize the prior plan to allow the new payer to access the member's clinical and treatment data.  The original payer's system provides an OAuth 2.0 token to the new plan.

2. Using the token, the new payer's system requests a [Coverage Transition document](#coverage-transition-document-structure) from the prior plan.  The prior plan either locates an existing document (previously prepared) or assembles the information needed and creates the document

3. The new payer system POSTs a Communication instance to the target payer system containing the requested FHIR document.

4. The new plan incorporates the information in the document into their utilization management/utilization review process for review and approval of the ongoing treatments for the new member.
 
The following diagram shows the workflow that is supported by this implementation guide:

{::options parse_block_html="false" /}
<figure>
  <img style="padding-bottom:30px" width="600px" src="workflow.png" alt="PCDE Workflow"/>
</figure>
{::options parse_block_html="true" /}

#### Authorization
This IG supplement PDex and uses the same authorization process for member directed exchange. By using the SMART on FHIR implementation of OAuth 2.0 and providing an appropriate access token from a prior prayer plan to the new plan, the new plan's application can authenticate to the prior plan and gain access to the specific member's data.

Since the CMS Notice of Proposed Rule Making requires that the member may direct the exchange of information from a prior plan for up to five years after a member leaves a plan, there may be the need to permit the current plan to access more than just the prior plan.  It may need to include a number of prior plans that fall within the 5 year time frame.  Target plans SHALL therefore allow the user to authenticate to multiple other payers to retrieve relevant data.

##### Handling Sensitive Data
The prior plan SHALL provide a method for the member to verify if it is ok to exchange sensitive data as defined by federal, state and, where appropriate, local statue. In the event that the member does not authorise exchanges sensitive data, the prior plan SHALL have a method to sequester such information and make it unavailable for exchange.  This SHOULD happen as part of the authorization process.

##### Token duration
In many cases, the Coverage Transition document either won't already exist or won't be up-to-date and some degree of manual work will be required by payer staff to gather content, organize it and provide appropriate document narrative will be required.  This may take hours or even a few days.  The duration of the authorization token SHALL therefore take this into account and allow sufficient time that the authorized access will not expire prior to the document being delivered.  (Allowance should also be made for a delay of 1-2 days in retrieval by the target system in the event of a plannned or unplanned system outage.)

#### Initiating document generation/retrieval
Once the necessary token has been retrieved by the OAuth process, the new payer system POSTs a [CDex CommunicationRequest](http://hl7.org/fhir/us/davinci-cdex/2019Jun/StructureDefinition-cdex-communicationrequest.html) to the original payer system.  It populates the [cdex-payload-clinical-note-type extension](http://hl7.org/fhir/us/davinci-cdex/2019Jun/StructureDefinition-cdex-communicationrequest-definitions.html#CommunicationRequest.payload.extension:payloadClinicalNoteType) with a code indicating the need for a [Coverage Transition document](#coverage-transition-document-structure).  (See [here](pcde-communicationrequest-example.html) for an example of the CommunicationRequest.)

#### Document delivery
Once the requested Coverage Transition document has been created, the original payer will POST a [CDex Communication](http://hl7.org/fhir/us/davinci-cdex/2019Jun/StructureDefinition-cdex-communication.html) to the new payer at the address specified in the original CommunicationRequest.  The payload.contentAttachment of the Communication will be a base64-encoded representation of the Coverage Transition document.  The mime type of the document SHALL be one of the syntaxes supported by the receiving payer as declared in their server's CapabilityStatement.  (Original payer systems SHALL support encoding documents in both XML and JSON.)  The POST transaction SHALL adhere to CDex security guidelines. <!-- TODO: It appears that neither CDex nor HRex actually *have* any security guidelines... --> (See [here](pcde-communication-example.html) for an example of the returned Communication.)


### Data structure
This IG uses a [FHIR document]({{site.data.fhir.path}}documents.html) approach to organizing the information shared by the original payer system.  This aligns with the approach typically used for any other sort of transition of responsibility (be it clinical or administrative responsibility).  The document approach allows arbitrary data objects (typically FHIR resources) in logical groupings for human consumption/review and allows human narrative to provide context and guidance on interpretting and using the information.

While the eventual target of future versions of this IG may be to allow fully automated import and use of the provided payer data, it is likely that at least some cases will always require human review and, in the short term, most/all payers will incorporate human review in the consumption of the data shared.  The document approach significantly simplifies this review process.

#### Coverage Transition document structure
All Coverage Transition documents SHALL comply with the [PCDE Bundle](profile-pcde-bundle.html) and [PCDE Composition](profile-composition.html) profiles.  These profiles set out a document structure as follows:

{::options parse_block_html="false" /}
<figure>
  <img style="padding-bottom:30px" width="250px" src="document.png" alt="Coverage Transition Document structure"/>
</figure>
{::options parse_block_html="true" /}

There are two major sections: [Active Treatments](#active-treatment) and [Other Documentation](#other-documentation)

##### Active Treatment
This section should repeat for each "active treatment" the patient is receiving under coverage from the original payer at the time the document is prepared.  Active treatments might include:
* medications
* procedures
* medical equipment
* enrollment in disease management programs
* etc.

Payer knowledge of what treatments are 'active' might come from prior authorizations, recently submitted claims or from clinical information shared with the payer as part of care management or other processes.

For each active treatment the following three sub-sections are available:

###### Treatment

This mandatory section defines a single treatment.  The [CarePlan](profile-careplan.html) instance that is the entry will convey the following information:
* What the treatment is (coded if possible, though text is permitted where a standardized procedure/medication/product code does not exist)
* The condition that caused the need for treatment (coded as ICD-10 if possible)
* Any protocol being followed in the treatment (referenced by URL)

###### Prior Coverage
This section will include the most recent relevant Claim(s) and/or Prior Authorization(s) associated with the treatment.  The purpose is to give a sense of how much of the treatments have been authorized, how many have already been performed/paid for, when the most recently billed occurrence was, etc.  Section narrative may provide additional detail.

Prior Authorizations SHOULD comply with the [PAS Prior Authorization](http://hl7.org/fhir/us/davinci-pas/2019Sep/profile-claim.html) and [PAS Prior Authorization Response](http://hl7.org/fhir/us/davinci-pas/2019Sep/profile-claimresponse.html) profile.  As yet, there are no Da Vinci profiles available for Claim resources, so technically any FHIR-valid Claim and ClaimResponse resources though alignment with the PAS Prior Authorization profiles is recommended.  The [US Core DocumentReference](http://hl7.org/fhir/us/core/STU3/StructureDefinition-us-core-documentreference.html) profile can be used to convey PDF or other non-discrete representations if full FHIR-encoding is not possible.

###### Supporting Information
These sections provide additional information that supports the treatment decision.  Examples might include past treatments that had been tried and proved ineffective, lab results or other reports that support the decision to perform the treatment, clinical orders that support the treatment, etc.

Each repetition must have one or more resource entries.  The section narrative SHALL provide a human description of the relevance of the contained resource entries.  Separate sections can be used to provide different groupings of resources that provide different types of support.  For example, one repetition might deal with prior therapy.  Another might contain the current order.  A third might indicate dispenses to date against that order

##### Other Documentation
These sections allow conveying information that is not directly related to a current active therapy but which may be relevant to allowing the new payer to provide appropriate care.  For example, conveying that a patient has had a double-mastectomy in the past.  That may not be related to 'active therapy', but would still be relevant in terms of the new payer knowing not to send reminders for mamogram treatments.

Multiple sections are allowed so that separate narratives can be used to group resources that are related together.  Narrative-only sections can be used to convey information for which no discrete data exists or is relevant.

#### Additional Notes
1. At the moment, this IG is quite flexible with how information is encoded.  Future versions of this IG will likely impose additional expectations, at least for certain types of conditions/therapies, as it becomes more obvious where additional interoperability requirements can result in less manual effort/reduced time in processing transitions in coverage.

2. The US Core profiles impose terminology expectations that reflect commonly used clinical terminologies.  These may not always align with commonly used billing codes.  While billing codes may be sent as well (as additional translations), payers SHALL translate to the appropriate clinical terminology (e.g. SNOMED CT) in order to comply with the US Core profiles if an appropriate code exists in the US Core value set.

3. The Coverage Transition document structure is open-ended.  Additional sections and sub-sections are permitted and additional data elements (including extensions) are permitted as part of the document instance.  Additional elements should never be used in place of 'standard' elements, but can be sent in addition to provide additional context to systems that understand the additional discrete data.  All important information should be reflected in the resource narrative (text) elements and/or in the section.text elements.