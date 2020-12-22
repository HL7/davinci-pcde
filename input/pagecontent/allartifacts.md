Complying with this implementation guide means complying with a number of profiles, extensions, value sets and custom search parameters.  This page provides an overview of where this information can be found.

The FHIR artifacts used by PCDE are organized according to whether the content developed as part of the US Core implementation guides or is Da Vinci-specific.

### Artifact Lists
<table>
  <tr>
    <td><a href="artifacts.html">PCDE-specific Artifacts</a></td>
  </tr>
  <tr>
    <td><a href="{{site.data.fhir.ver.uscore}}/profiles.html">US Core (3.1.0 - R4 based)</a></td>
  </tr>
  <tr>
    <td><a href="{{site.data.fhir.ver.hrex}}/artifacts.html">Da Vinci Health Record Exchange (HRex) (0.2.0 - Sept 2020 ballot)</a></td>
  </tr>
  <tr>
    <td><a href="{{site.data.fhir.ver.pdex}}/artifacts.html#structures-resource-profiles">Da Vinci Payer Data Exchange (PDex) (1.0.0 STU1)</a></td>
  </tr>
  <tr>
    <td><a href="{{site.data.fhir.ver.pas}}/artifacts.html">Da Vinci Prior Authorization Support (PAS) (1.0.0 - STU1)</a></td>
  <tr>
    <td><a href="{{site.data.fhir.ver.subscription}}/artifacts.html">Subscription Backport (Jan 2021 ballot)</a></td>
  </tr>
</table>

Additional information about the use of these artifacts can be found in the [main specification](spec.html#profiles).


The artifacts are of four types:

* [Profiles]({{site.data.fhir.path}}profiling.html) constrain FHIR resources to reflect PCDE requirements
* [Extensions]({{site.data.fhir.path}}extensibility.html) define additional data elements that can be conveyed as part of a resource
* [Code Systems]({{site.data.fhir.path}}codesystem.html) define PCDE-specific terminologies to be used in one or more of those profiles
* [Value Sets]({{site.data.fhir.path}}valueset.html) define the specific subsets of both PCDE-defined and other code systems that can be (or are recommended to be) used within one or more profile elements
* [Operations]({{site.data.fhir.path}}operationdefinition.html) of which there is only one which defines the PCDE-specific constraints on the Claim submit operation.

<!-- Todo: examples, capabilitystatement, TestScenario? -->
