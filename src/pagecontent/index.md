{% raw %}
<blockquote class="stu-note">
<p>
This specification is currently undergoing ballot and connectathon testing.  It is expected to evolve, possibly significantly, as part of that process.
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
Individuals interested in participating in the Prior Authorization Support or other HL7 Da Vinci projects can find information about Da Vinci [here](http://www.hl7.org/about/davinci).
</p>
</blockquote>
{% endraw %}

### Overview
When patients move from one payer's health insurance plan to another, the new payer needs to ensure that claims are for medically necessary and appropriate services - but they often don't have the information needed to make those assessments.  As a result, patients can face a break in continuity of care and increased costs as tests are re-run or prior therapies need to be re-tested in order to comply with the rules of the new payer.  By enabling a patient-authorized transfer of information from the original payer to the new payer, the new payer can have access to information about what therapies are currently in place and the rationale for them, as well as what precursor steps have been taken to demonstrate the medical necessity and appropriateness of the current therapy.  By automating this exchange and maximizing the computability of the information shared, a process that frequently takes weeks or months can be reduced to a few days - reducing costs and improving patient care quality and experience.

This implementation guide defines standardized mechanisms for a patient to enable a transfer of "current care" and other relevant coverage-related information from an original payer to a new payer and also defines a standardized structure for organizing and encoding that information to ease it's consumption by the new payer organization.

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
* **[US Core STU3](http://hl7.org/fhir/us/core/STU3)** - The balloted version of US Core based on FHIR R4.  This implementation guide will be updated to be based on the final R4 release once it is published.
* **[Da Vinci PDex July 2019 ballot](http://hl7.org/fhir/us/davinci-pdex/2019Jun)** - The balloted version of Da Vinci Payer Data Exchange IG.  This implementation guide will be updated to be based on the official published version of this IG once the ballot process is complete.
* **[Da Vinci CDex July 2019 ballot](http://hl7.org/fhir/us/davinci-cdex/2019Jun)** - The balloted version of Da Vinci Clinical Data Exchange IG.  This implementation guide will be updated to be based on the official published version of this IG once the ballot process is complete.
* **[Da Vinci HRex July 2019 ballot](http://hl7.org/fhir/us/davinci-hrex/2019Jun)** - The balloted version of Da Vinci Health Record Exchange IG.  This implementation guide will be updated to be based on the official published version of this IG once the ballot process is complete.
* **[Da Vinci PAS Sept 2019 ballot](http://hl7.org/fhir/us/davinci-pas/2019Sep)** - The balloted version of Da Vinci Prior Authorization Support IG.  This implementation guide will be updated to be based on the official published version of this IG once the ballot process is complete.

This implementation guide defines additional constraints and usage expectations above and beyond the information found in these base specifications.