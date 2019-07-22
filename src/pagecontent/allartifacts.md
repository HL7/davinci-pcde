Complying with this implementation guide means complying with a number of profiles, extensions, value sets and custom search parameters.  This page provides an overview of where this information can be found.

The FHIR artifacts used by PCDE are organized according to whether the content developed as part of the US Core implementation guides or is Da Vinci-specific.

### Artifact Lists
<table>
  <tr>
    <td><a href="artifacts.html">PCDE-specific Artifacts</a></td>
  </tr>
  <tr>
    <td><a href="http://hl7.org/fhir/us/core/STU3">US Core (3.0.0 - R4 based)</a></td>
  </tr>
  <tr>
    <td><a href="http://hl7.org/fhir/us/davinci-pdex/2019Jun/profiles.html">Da Vinci Payer Data Exchange (PDex) (0.1.0 July 2019 ballot)</a></td>
  </tr>
  <tr>
    <td><a href="http://hl7.org/fhir/us/davinci-cdex/2019Jun/profiles.html">Da Vinci Clinical Data Exchange (CDex) (0.1.0 July 2019 ballot)</a></td>
  </tr>
  <tr>
    <td><a href="http://hl7.org/fhir/us/davinci-hrex/2019Jun/profiles.html">Da Vinci Health Record Exchange (HRex) (0.1.0 - July 2019 ballot)</a></td>
  </tr>
  <tr>
    <td><a href="http://hl7.org/fhir/us/davinci-pas/2019Sep/artifacts.html">Da Vinci Prior Authorization Support (PAS) (0.1.0 - Sept 2019 ballot)</a></td>
  </tr>
</table>

Additional information about the use of these artifacts can be found in the [main specification](spec.html#profiles).


The artifacts are of four types:

* [Profiles]({{site.data.fhir.path}}profiling.html) constrain FHIR resources to reflect PAS requirements
* [Extensions]({{site.data.fhir.path}}extensibility.html) define additional data elements that can be conveyed as part of a resource
* [Code Systems]({{site.data.fhir.path}}codesystem.html) define PAS-specific terminologies to be used in one or more of those profiles
* [Value Sets]({{site.data.fhir.path}}valueset.html) define the specific subsets of both PAS-defined and other code systems that can be (or are recommended to be) used within one or more profile elements
* [Operations]({{site.data.fhir.path}}operationdefinition.html) of which there is only one which defines the PAS-specific constraints on the Claim submit operation.

<!-- Todo: examples, capabilitystatement, TestScenario? -->