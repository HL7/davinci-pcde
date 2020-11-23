This page is broken into three sections which outline the expectations for systems wishing to comply with the IG:
* General [context](#context) that implementers need to understand in reading the remainder of the guide

* A description of the technical [data exchange](#data-exchange) mechanisms by which a patient requests and enables that information to be transmitted from the original payer to the new payer and the means by which that transfer actually occurs.

* A description of the [data structure](#data-structure) to be shared that supports a transition of coverage responsibility from one payer plan to another

### Context
#### Pre-reading
Before reading this formal specification, implementers should first familiarize themselves with two other key portions of the specification:

* The [Use Cases & Overview](usecases.html) page provides context for what this formal specification is trying to accomplish and will give a sense of both the business context and general process flow enabled by the formal specification below.

* The [Technical Background](background.html) page provides information about the underlying specifications and indicates what portions of them should be read and understood to have necessary foundation to understand the constraints and usage guidance described here.

#### Conformance and Conventions
This implementation guide adheres to the conformance documentation conventions found in the [HRex Conformance Expecations page]({{site.data.fhir.ver.hrex}}/conformance.html), including expectations around the meaning of 'Must Support'.

#### Profiles
This specification makes significant use of [FHIR profiles]({{site.data.fhir.path}}profiling.html) and terminology artifacts to describe the content to be shared as part of prior authorization requests and responses.

The full set of profiles defined in or used by this implementation guide can be found by following the links on the [Artifacts](allartifacts.html) page.

#### Terminology
This IG defines custom codes for document types and sections.  Implementers **SHALL** consider the codes 'temporary'.  After implementation testing and confirmation, these custom codes will migrate to standardized codes in an official code system - most likely LOINC.

### Data Exchange
This data exchange builds on the Da Vinci [Health Record Exchange (HRex)]({{site.data.fhir.ver.hrex}}) and [Payer Data Exchange (PDex)]({{site.data.fhir.ver.pdex}}) implementation guides, leveraging the [OAuth 2.0-based]({{site.data.fhir.ver.pdex}}/3-4_Interaction_Methods.html#3-4-2-oauth20-and-fhir-api) mechanism to enable data flow between two payer systems and the [Task-based requested exchange]({{site.data.fhir.ver.hrex}}/exchanging-request.html#task) mechanism to request the desired document.  This section of the implementation guide provides details on that flow.

#### Pre-conditions
For this implementation guide to be applicable, the following conditions must be met:

* A member of a covered plan has enrolled in another covered plan offered by another payer.

* That member is currently being treated for some chronic or acute condition and wishes the treatment to continue.

* The new payer has performed a patient / coverage resolution process and has information about relevant prior coverage.

* Both the new payer and the old payer must be able to recognize each other based on an agreed payer identifier scheme.

* The new payer system knows the base FHIR API URL for the original payer endpoint and has basic business arrangements in place allowing query of data from it.

NOTE: Work to standardize how payer identification will be managed, as well as how the FHIR endpoint for a given payer will be found (e.g. through the use of a registry) is ongoing.  For now, this is left to site-to-site negotiation.

#### Privacy & Security
This implementation guide inherits all of the requirements and guidance defined in the [HRex Security and Privacy page]({{site.data.fhir.ver.hrex}}/security.html).  Conformant systems must familiarize themselves with and abide by the expectations established there for all functions enabled by this implementation guide.

#### Workflow
1. The new payer executes a [Member Match]({{site.data.fhir.ver.pdex}}/OperationDefinition-member-match.html) operation using the member's old insurance information and demographics to determine the patient identifier on the original payer's system.

2. Optional: The member uses an interface/portal or SMART app within the new payer's system to authenticate to the original payer's system and authorize the prior payer to allow the new payer to access the member's clinical and treatment data.  The original payer's system provides an OAuth 2.0 token to the new plan.

3. The new payer's system (possibly using the token provided in #2) requests a [Coverage Transition Document](#coverage-transition-document-structure) from the prior plan by POSTing a [Task](StructureDefinition-pcde-task-request.html) to their system.  Optionally, the new payer creates a [Subscription](#subscription) on the old payers system, requesting notifications about updates to the newly created Task.

4. The source (original) payer either locates an existing document (previously prepared) or assembles the information needed and creates the requested document.  Updates are made to the `Task.status` element and, optionally, human-readable status information within `Task.businessStatus.text` element as the task progresses through different [states](https://www.hl7.org/fhir/task.html#statemachine).  (In PCDE, the Task is limited to the states 'requested', 'in-progress', 'completed' or 'failed'.)  The original payer updates `Task.output` with a reference to the document when `Task.status` is completed.

5. The new payer queries the origninal payer's system for updates to the Task.  This is either done by polling at regular intervals or by responding to a Subscription notification.  When the `Task.status` is completed, the new payer retrieves the [Coverage Transition Document](#coverage-transition-document-structure) pointed to by the `Task.output`.

6. The new payer incorporates the information in the document into their utilization management / utilization review process for review and approval of the ongoing treatments for the new member.

The following diagram illustrates the workflow that is supported by this implementation guide:

{::options parse_block_html="false" /}
<figure>
  <img style="padding-bottom:30px" width="600px" src="workflow.png" alt="PCDE Workflow"/>
</figure>
{::options parse_block_html="true" /}

#### Authorization
This IG supplements PDex and uses the same authorization process for member directed exchange. By using the SMART on FHIR implementation of OAuth 2.0 and providing an appropriate access token from an original payer's system to a new payer at the request of a member, the new payer's application is able to authenticate to the original payer's system and gain access to information authorized by the member.

Since the CMS Notice of Proposed Rule Making requires that the member may direct the exchange of information from payers for up to five years after a member leaves a plan, there may be the need to permit new payers to access more than just the prior plan.  It may be necessary to include a number of prior plans that fall within the 5 year time frame.  Payer systems **SHALL** therefore allow members to authorize multiple other payers to retrieve relevant information.  Note that while regulations may establish a minimum period of time over which payers must provide data, this guide establishes no maximum.  Payers are free to share information relevant to transition of coverage that covers a longer period of time if it is available.

##### Handling Sensitive Data
Payers **SHALL** provide a method for past members to authorize the exchange sensitive information as defined by federal, state and, where appropriate, local statue.  In the event that a member does not permit a payer to exchange sensitive data, the payer **SHALL** have a method to sequester such information and make it unavailable for exchange.  This **SHOULD** happen as part of the authorization process.

##### Token duration
The Token, if one is used at all, is only used to authorize the initial creation of the Task.  It is not necessary to provide the token when establishing a subscription or polling to monitor the Task or to retrieve the Task and its linked document.  Payers **SHALL** ensure that only authorized systems (typically just the requesting payer) are able to access PCDE Tasks and any coverage transition document created as a result.

#### Initiating document generation/retrieval
Once the necessary token has been retrieved through the OAuth process, the new payer system will POST a [PCDE Task](StructureDefinition-pcde-task-request.html) request to the original payer system.  The requester **SHALL** populate the `Task.code` and `Task.status` to indicate that a [Coverage Transition document](#coverage-transition-document-structure) is requested.  (See [here](Task-requested.html) for an example of a requested Task.)

If the receiving system does not recognize the member identified in Task.for or there are other structural issues with that Task (e.g. requesting payer is not recognized, Task doesn't comply with profile, etc.), then the original payer SHALL respond with an appropriate 4xx or 5xx HTTP error accompanied by an [OperationOutcome]({{site.data.fhir.path}}operationoutcome.html) conveying the reason for failure.

#### Subscription
Payers **MAY** support [subscriptions]({{site.data.fhir.ver.hrex}}/exchanging-subscription.html) to allow monitoring of changes to the Task resource rather than relying on [polling]({{site.data.fhir.ver.hrex}}/exchanging-polling.html).  Payers making use of subscription SHOULD comply with the [Subscription Backport IG]({{site.data.fhir.ver.subscription}}) which allows pre-adoption of R5 subscription mechanisms in R4-conformant systems.

#### Tracking Status
While fulfilling the request, the original payer **MAY** update `Task.status` or `Task.businessStatus.text` to reflect interim status information.  (For example, indicating that the task is in-progress with a date when the document is expected to be ready.)  An example can be seen [here](Task-in-progress.html).

When the document is available, original payer **SHALL** update `Task.status` to `completed` and provide a reference to the [Coverage Transition document](#coverage-transition-document-structure) resource in `Task.output`.    An example can be seen [here](Task-completed.html).

The payer may be unable to complete a request if the member is unknown or the information requested cannot be provided.  In these cases, the original payer **SHALL** indicate that a requests was not completed by updating `Task.status` to `failed` and, optionally, `Task.statusReason`.  An example can be seen [here](Task-failed.html).

There are two options for monitoring the Task to verify acceptance, determine progress and determine if the requested document is ready for retrieval: subscription and polling.

##### Subscription
At the time of this publication, the expectations around the use of subscription within U.S. Core for use with R4 have not yet been finalized.  (They are expected to use a pre-adopted version of the R5 subscription mechanism which is significantly revised from the version published as part of R4.)  This specification will be updated once those requirements have stabilized.  In the meantime, payers are permitted to pre-adopt the use of subscription, but will need to negotiate the fine details of how it will work.

##### Polling
If no subscription was created, the new payer **SHALL** routinely perform a read on the orginal payer's system using the id of the originally created Taskto receive updates to the Task.  Polling is performed by executing a `GET` operation using the `id` received from the original payer when the Task was created, optionally using the `If-Modified-Since` header to limit the data returned if the Task has not been updated since it was last polled.

Example:
```
GET [base]/Task/1135804
```

When the document is available, original payer **SHALL** update `Task.status` to `completed` and provide a reference to the [Coverage Transition document](#coverage-transition-document-structure) resource in `Task.output`.

#### Retrieving the document
When the `Task.status` is `completed`, the Task will have an output element with the name 'document' that is a reference to the requested coverage transition document.  On retrieving the Task and finding it in completed state, the new payer **SHALL** perform a 'read' on the specified URL for the document referenced in the `Task.output` element for the newly created document, again using the previously supplied token.  The new payer can then process the document contents as necessry to ensure a smooth transition of care.

NOTES:

* This specification does not, itself, impose any expectation on the duration of records retention after a patient's coverage ceases, only that the payer expose all relevant information it still retains. If no details are retained, but the payer still recognizes the patient, the Task should be changed to a status of 'Failed' with a Task.statusReason indicating that no records remain to be shared.

* The original payer **SHALL** make the Task resource and associated document available for a minimum of 7 days after transitioning to complete, or for at least 24 hours after a successful response is returned to a 'read' attempt by the new payer, whichever is less.  This ensures the new payer has an opportunity to re-query the document if an issue occurs during the original read.

* It is theoretically possible for multiple Tasks to point to the same document (e.g. if a patient is transitioning to coverage under two different plans, the payer **MAY** simply update the second Task to point to a coverage transition document originally created for another payer).


### Data structure
This IG uses a [FHIR document]({{site.data.fhir.path}}documents.html) approach to organizing the information shared by the original payer system.  This aligns with the approach typically used for any other sort of transition of responsibility (be it clinical or administrative responsibility).  The document approach allows arbitrary data objects (typically FHIR resources) in logical groupings for human consumption/review and allows human narrative to provide context and guidance on interpretting and using the information.

The original payer SHOULD send all information they have available they believe to be reasonably necessary for the new payer to make a determination of medical necessity.  (Future versions of this specification are expected to tighten this requirement to SHALL.)

While the eventual target of future versions of this IG may be to allow fully automated import and use of the provided payer data, it is likely that at least some cases will always require human review and, in the short term, most/all payers will incorporate human review in the consumption of the data shared.  The document approach significantly simplifies this review process.

#### Coverage Transition document structure
All Coverage Transition documents SHALL comply with the [PCDE Bundle](StructureDefinition-profile-pcde-bundle.html) and [PCDE Composition](StructureDefinition-profile-composition.html) profiles.  These profiles set out a document structure as follows:

{::options parse_block_html="false" /}
<figure>
  <img style="padding-bottom:30px" width="250px" src="document.png" alt="Coverage Transition Document structure"/>
</figure>
{::options parse_block_html="true" /}

An example of a Coverage Transition document can be found [here](Bundle-pcde-document.html).

There are two major sections to a Coverage Transition Document: [Active Treatments](#active-treatment) and [Other Documentation](#other-documentation)

##### Active Treatment
This section should repeat for each "active treatment" the patient is receiving under coverage from the original payer at the time the document is prepared.  Active treatments include any treatment that, to the best of the payer's knowledge, is ongoing and for which the new payer might reasonably expect prior authorizations or claims, such as:

* medications
* procedures
* medical equipment
* enrollment in disease management programs
* etc.

Payer knowledge of what treatments are 'active' might come from prior authorizations, recently submitted claims or from clinical information shared with the payer as part of care management or other processes.

For each active treatment the following three sub-sections are available:

###### Treatment

This mandatory section defines a single treatment.  The [CarePlan](StructureDefinition-profile-careplan.html) instance that is the entry will convey the following information:
* What the treatment is (coded if possible, though text is permitted where a standardized procedure/medication/product code does not exist)
* The condition that caused the need for treatment (coded as ICD-10-CM if possible)
* Any protocol being followed in the treatment (referenced by URL)

###### Prior Coverage
This section will include the most recent relevant Claim(s) and/or Prior Authorization(s) associated with the treatment.  The purpose is to give a sense of how much of the treatments have been authorized, how many have already been performed/paid for, when the most recently billed occurrence was, etc.  Section narrative may provide additional detail.

Prior Authorizations SHOULD comply with the [PAS Prior Authorization](http://hl7.org/fhir/us/davinci-pas/2019Sep/profile-claim.html) and [PAS Prior Authorization Response](http://hl7.org/fhir/us/davinci-pas/2019Sep/profile-claimresponse.html) profile.  As yet, there are no Da Vinci profiles available for Claim resources, so technically any FHIR-valid Claim and ClaimResponse resources are allowed, though alignment with the PAS Prior Authorization profiles is recommended.  The [US Core DocumentReference](http://hl7.org/fhir/us/core/STU3.1/StructureDefinition-us-core-documentreference.html) profile can be used to convey PDF or other non-discrete representations if full FHIR-encoding is not possible.

###### Supporting Information
These sections provide additional information that supports the treatment decision.  Examples might include past treatments that had been tried and proved ineffective, lab results or other reports that support the decision to perform the treatment, clinical orders that support the treatment, etc.

Each repetition must have one or more resource entries.  The section narrative SHALL provide a human description of the relevance of the contained resource entries.  Separate sections can be used to provide different groupings of resources that provide different types of support.  For example, one repetition might deal with prior therapy.  Another might contain the current order.  A third might indicate dispenses to date against that order.

While it is technically possible to send raw NCPDP, X12 or other content in this specification wrapped using DocumentReference, the general expectation is that relevant claims information will be exposed as appropriate FHIR resources (e.g. Encounter, Procedure, etc.). In cases where discrete information can't be appropriately exposed in FHIR, PDFs wrapped in DocumentReference are also an option.

When interpreting information shared in a coverage transfer document, it is often helpful to understand the source and history of the information being shared.  This also allows the receiving payer to better reconcile the shared information with data shared from other sources.  For this reason, resources shared as 'supporting' information SHOULD be accompanied by Provenance instances complying with the [PDex Provenance profile]({{site.data.fhir.ver.pdex}}/StructureDefinition-pdex-provenance.html) as additional 'supporting information' entries.

##### Other Documentation
These sections allow conveying information that is not directly related to a current active therapy but which may be relevant to allowing the new payer to provide appropriate care.  For example, conveying that a patient has had a double-mastectomy in the past.  That may not be related to 'active therapy', but would still be relevant in terms of the new payer knowing not to send reminders for mamogram treatments.

Multiple sections are allowed so that separate narratives can be used to group resources that are related together.  Narrative-only sections can be used to convey information for which no discrete data exists or is relevant.

#### Additional Notes
1.  The exchanges covered by this implementation guide are subject to appropriate regulation regarding operations and consent, including HIPAA and 42CFR Part II privacy standards. The entities participating in these exchanges should be familiar with and adhere to these and other relevant regulatory requirements.

2. At the moment, this IG is quite flexible with how information is encoded.  Future versions of this IG will likely impose additional expectations, at least for certain types of conditions/therapies, as it becomes more obvious where additional interoperability requirements can result in less manual effort/reduced time in processing transitions in coverage.

3. The US Core profiles impose terminology expectations that reflect commonly used clinical terminologies.  These may not always align with commonly used billing codes.  While billing codes may be sent as well (as additional translations), payers SHALL translate to the appropriate clinical terminology (e.g. NDC or HCPCS drug codes codes to RxNorm) in order to comply with the US Core profiles if an appropriate code exists in the US Core value set.

4. The Coverage Transition document structure is open-ended.  Additional sections and sub-sections are permitted and additional data elements (including extensions) are permitted as part of the document instance.  Additional elements should never be used in place of 'standard' elements, but can be sent in addition to provide additional context to systems that understand the additional discrete data.  All important information should be reflected in the resource narrative (text) elements and/or in the section.text elements.
