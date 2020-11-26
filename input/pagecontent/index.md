{% raw %}
<blockquote class="stu-note">
<p>
This is the initial official release of this specification.  It includes feedback from both ballot review and connectathon testing.  However, it will continue to evolve based on implementer feedback.
</p>
<p>
Feedback is welcome and may be submitted through the <a href="http://hl7.org/fhir-issues">FHIR change tracker</a> indicating "US Da Vinci PCDE" as the specification.
</p>
<p>
This implementation guide is dependent on other specifications.  Please submit any comments you have on these base specifications as follows:
</p>
<ul>
  <li>Feedback on the FHIR core specification should be submitted to the <a href="http://hl7.org/fhir-issues">FHIR change tracker</a> with "FHIR Core" as the specification.</li>
  <li>Feedback on the US core profiles should be submitted to the <a href="http://hl7.org/fhir-issues">FHIR change tracker</a> with "US Core" as the specification.</li>
  <li>Feedback on profiles from one of the other Da Vinci implementation guides should also be submitted to the <a href="http://hl7.org/fhir-issues">FHIR change tracker</a> with the specification set to the appropriate IG.</li>
</ul>
<p>
Individuals interested in participating in the Prior Authorization Support or other HL7 Da Vinci projects can find information about Da Vinci <a href="http://www.hl7.org/about/davinci">here</a>.
</p>
</blockquote>
{% endraw %}

### Overview
The United States has one of the world's most complex healthcare systems with provision of care crossing multiple venues, clinical settings, and geographic boundaries. The healthcare services offered vary across organizations, and the terminology, standards and equipment involved often vary as well.  Transitions of care are therefore complex and provide frequent opportunities for medical errors, redundant care, and care gaps. Despite federal regulatory policy mandating data exchange between care providers, there remain serious problems with timely and comprehensive data exchange. Beyond issues with disconnected data transmission across care providers, patients generally have multiple payers including their role as a self-payer. As with care provider coordination, payer coordination is also essential to care continuity. For example, a patient who relies on home oxygen should not be at risk of hospitalization because a new payer is not aware of the requirement until after the oxygen runs out.

This need for continuity across payers has been recognized also by the United States' largest payer, the Centers for Medicare and Medicaid Services (CMS), who has proposed in 2019 ([Federal Register :: Medicare and Medicaid Programs; Patient Protection and Affordable Care Act; Interoperability and Patient Access for Medicare Advantage Organization and Medicaid Managed Care Plans, State Medicaid Agencies, CHIP Agencies and CHIP Managed Care Entities, Issuers of Qualified Health Plans in the Federally-Facilitated Exchanges and Health Care Providers](https://www.federalregister.gov/documents/2019/03/04/2019-02200/medicare-and-medicaid-programs-patient-protection-and-affordable-care-act-interoperability-and)) to require the exchange of data about ongoing and planned treatments covered by a previous payer and send it to a current payer along with the metadata around the conditions they are treating, as well as how the determination was made. While this did not make it into the CMS Interoperability final rule ([Federal Register :: Medicare and Medicaid Programs; Patient Protection and Affordable Care Act; Interoperability and Patient Access for Medicare Advantage Organization and Medicaid Managed Care Plans, State Medicaid Agencies, CHIP Agencies and CHIP Managed Care Entities, Issuers of Qualified Health Plans on the Federally-Facilitated Exchanges, and Health Care Providers](https://www.federalregister.gov/documents/2020/05/01/2020-05050/medicare-and-medicaid-programs-patient-protection-and-affordable-care-act-interoperability-and)) , the need still exists to support continuity of care when a member transitions coverage form one payer to another.

It would be reasonable for payers to use this IG for exchanges outside of the required plans if desired. Optimally, the routine and electronic exchange of this information would minimize the need for additional information to be obtained from patients and other providers and ensure that critical services and therapies are continued without interruption. While for large scale payer transitions, such as employer plan transitions, there have been some efforts to provide coordination of care and information exchange, there are no known formal efforts to support information and care exchange in a systematic way at the payer level.

The DaVinci Project is already supporting the exchange of payer information for prior authorization, formulary information sharing, provider directory and other member-related health information. Furthermore, there are aspects of the need to exchange active treatment plans and decisions which should align to use cases beyond the payer exchange use case. The coverage decision exchange document allows the capture of relevant clinical information about decisions, treatments, and their context using CarePlan. This aligns with the approach that clinicians and clinical systems take to describe this same content during transitions of care. This should make it easier to integrate information received by payers from providers.

The form and manner of the content in the CarePlan is aligned to the regulatory framework of data exchange at transitions of care including Goals and Treatment Plan as well as the United States Core Data for Interoperability (<a href="https://www.healthit.gov/isa/united-states-core-data-interoperability-uscdi?">USCDI</a>) mandated by the Office of the National Coordinator for Health IT (ONC) and the Center for Medicated Services (CMS).  The data shared within the document will depend on the nature of the patient's past care, but could include past prior authorizations and claims, information about past meds and treatments, recent and historical lab results, implanted or supplied devices, etc.

For this use case, we address the need for continuity of treatment when patients move from one payer's health insurance plan to another.  In the current situation, the patient and new payers often do not have the information needed to continue treatment in progress.  As a result, patients can face a break in continuity of care, challenges to share additional information, and increased costs as tests are re-run or prior therapies need to be re-tested in order to comply with the rules of the new payer.  By enabling an authorized transfer of information from the original payer to the new payer, the new payer can have access to information about what therapies are currently in place and the rationale for them, as well as what precursor steps have been taken to demonstrate the medical necessity and appropriateness of the current therapy.  By automating this exchange and maximizing the computability of the information shared, a process that frequently takes weeks or months can be reduced to a few days or even minutes - reducing costs and improving patient safety, quality and experience.

This implementation guide defines standardized mechanisms for a patient or payer to enable a transfer of "current active treatments" with other relevant metadata and coverage-related information from a prior payer to a new payer. For example, gender transition information, amputations or other past medical history that may impact the evaluation of the appropriateness of future claims. It also defines a standardized structure for organizing and encoding that information to ease its consumption by the new payer organization.

Obviously, actual continuity of care will be governed by the policies of the new payer.  It is possible that the patient's new coverage will not cover previous therapies or that additional information will be required that is not available from the original payer.  However, disruption in continuity of care due to the new payer being unaware of information held by the original payer should be significantly diminished through the adoption of this implementation guide.

### Content and organization
The implementation guide is organized into the following sections:

* [Use Cases and Overview](usecases.html) describes the intent of the implementation guide, gives examples of its use and provides a high-level overview of expected process flow
* [Technical Background](background.html) describes the different specifications this implementation guide relies on and indicates what developers should read and understand prior to implementing this specification
* [Formal Specification](spec.html) covers the detailed implementation requirements and conformance expectations
* [Artifacts](allartifacts.html) introduces and provides links to the FHIR [R4](artifacts.html) profiles, operations and other FHIR artifacts used in this implementation guide
* [Downloads](downloads.html) allows download of this and other specifications as well as other useful files
* [Credits](credits.html) identifies the individuals and organizations involved in developing this implementation guide


### Dependencies
This implementation guide relies on the following other specifications:
* **[FHIR R4]({{site.data.fhir.path}})** - The 'current' official version of FHIR as of the time this implementation guide was published.  See the [background page](background.html#fhir) for key pieces of this specification implementers should be familiar with.
* **[US Core STU3.1]({{site.data.fhir.ver.uscore}})** - The most recent official release of US Core (based on FHIR R4).
* **[Da Vinci HRex Sept. 2020 ballot]({{site.data.fhir.ver.hrex}})** - The balloted version of Da Vinci Health Record Exchange IG.  This implementation guide will be updated to be based on the official published version of this IG once the ballot process is complete.
* **[Da Vinci PDex July STU 1]({{site.data.fhir.ver.pdex)** - The balloted version of Da Vinci Payer Data Exchange IG.  This implementation guide will be updated to be based on the official published version of this IG once the ballot process is complete.
* **[Da Vinci PAS Sept. STU 1]({{site.data.fhir.ver.pas}})** - The balloted version of Da Vinci Prior Authorization Support IG.  This implementation guide will be updated to be based on the official published version of this IG once the ballot process is complete.
* **[Subscription Backport Jan 2021 ballot]({{site.data.fhir.ver.subscription}})** - The balloted version of the R4 backport of R5 subscriptoin capabilities.  This implementation guide will be updated to be based on the official published version of this IG once the ballot process is complete.

This implementation guide defines additional constraints and usage expectations above and beyond the information found in these base specifications.


### Implementation Guidance
In addition to this implementation guide, Da Vinci maintains a more dynamic 'supplemental' implementation guide containing additional examples and specific advice about how to best construct Coverage Transition documents for various real-world scenarios.  This supplemental material can be found [here](https://confluence.hl7.org/display/DVP/PCDE+Supplemental+Guide) on HL7's Confluence site.