### Da Vinci
Da Vinci is an industry sponsored project that brings together the U.S. EHR vendor and Payer communities to help payers and providers to positively impact clinical, quality, cost, and care management outcomes using FHIR-related technologies.  The project organizes meetings (face-to-face and conference calls) as well as connectathons to find ways to leverage FHIR technologies to support and integrate value-based care (VBC) data exchange across communities. Da Vinci identifies value-based care use cases of interest to its member and the community as a whole.

The process that Da Vinci has adopted includes:
1. identify business, clinical, technical and testing requirements,
2. develop and ballot a FHIR based implementation guide (IG),
3. develop a reference implementation (RI) that is used to demonstrate that the concepts in the IG are possible to implement,
4. pilot the standard
5. support the production use of the IG to enable exchange of data to support interoperability for value-based care.

Additional information about Da Vinci, its members, the use cases and the implementation guides being developed can all be found on the [HL7 website](http://www.hl7.org/about/davinci). Meeting minutes and other materials can be found on the [Da Vinci Confluence page](https://confluence.hl7.org/display/DVP).

### Systems
The PCDE implementation guide defines the responsibilities of the two types of systems involved in a PCDE solution:

**Original payer systems** Systems that manage data on behalf of a payer who is a source for data to be transferred

**New payer systems** Systems that manage data on behalf of a payer who is an intended recipient of transferred data.

### Underlying technologies
This guide is based on the [HL7 FHIR]({{site.data.fhir.path}}index.html) standard.  Implementers of this specification therefore need to understand some basic information about these specifications.

#### FHIR
This implementation guide uses terminology, notations and design principles that are
specific to FHIR.  Before reading this implementation guide, it's important to be familiar with some of the basic principles of FHIR as well
as general guidance on how to read FHIR specifications.  Readers who are unfamiliar with FHIR are encouraged to read (or at least skim) the following
prior to reading the rest of this implementation guide.

* [FHIR overview]({{site.data.fhir.path}}overview.html)
* [Developer's introduction]({{site.data.fhir.path}}overview-dev.html) (or [Clinical introduction]({{site.data.fhir.path}}overview-clinical.html))
* [FHIR data types]({{site.data.fhir.path}}datatypes.html)
* [Using codes]({{site.data.fhir.path}}terminologies.html)
* [References between resources]({{site.data.fhir.path}}references.html)
* [How to read resource & profile definitions]({{site.data.fhir.path}}formats.html)
* [Base resource]({{site.data.fhir.path}}resource.html)

This implementation guide supports the [R4]({{site.data.fhir.path}}index.html) version of the FHIR standard. R4 is just recently published and the goal is to ensure the implementation guide is aligned with the current direction of the FHIR standard.

Because of this IG's focus on document creation, implementers should also familiarize themselves with the FHIR [Composition]({{site.data.fhir.path}}composition.html) and the guidelines around the creation and processing of [FHIR documents]({{site.data.fhir.path}}documents.html).

This implementation guide  builds on the following IGs:
* [Da Vinci Health Record Exchange (HRex)]({{site.data.fhir.ver.hrex}})
* [Da Vinci Health Record Exchange (PDex)]({{site.data.fhir.ver.pdex}})
* [Da Vinci Prior Authorization Support (PAS)]({{site.data.fhir.ver.pas}})
* [US Core 3.1 (R4-based)]({{site.data.fhir.ver.uscore}})

Implementers should familiarize themselves with the [Task-based]({{site.data.fhir.ver.hrex}}/exchanging-request.html#requesting-exchange-using-task) communication mechanism and the [member match]({{site.data.fhir.ver.hrex}}/OperationDefinition-member-match.html) operation defined within HRex and the profiles defined in that IG as well as all of the others.
