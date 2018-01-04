---
title: Design | Search Exemption Status
keywords: development
tags: [design,development]
sidebar: overview_sidebar
permalink: build_exemption_search.html
summary: "How to use FHIR Prescription Exemption resources to view exemption status"
---

{% include custom/search.warnbanner.html %}

## Prerequisites ##

To use this API the requester:

* SHALL have gone through accreditation and received an endpoint certificate and associated ASID (Accredited System ID) for the client system with this API interaction registered.

* SHALL have either:

  - Authenticated the user using national smartcard authentication, and obtained a the UUID from the user’s smartcard (and associated RBAC role from CIS), or

  - Authenticated the user using an assured local mechanism, and obtained a local user ID and role
and pass this user information in a JSON web token - see Cross Organisation Audit and Provenance for details.

* SHALL have either:
  - Previously traced the patient’s NHS Number using PDS or an equivalent service, or
  - Retrieved a validated NHS number from an electronic prescription message.

## Search for Prescription Exemptions ##

Prescription Exemptions can be confirmed by searching for a Prescription Exemption Observation for a specified patient using a business identifier (i.e. the NHS Number).

```
GET [base]/Observation?subject:Patient.identifier=[system]|[value]&code=[system]|[code]
```

* The `[system]` field for the Patient Identifier SHALL be populated with the identifier system URL: https://fhir.nhs.uk/Id/nhs-number.
* The `[system]` field for the Code SHALL be populated with the identifier system URL: https://fhir.nhs.uk/fhir-observation-code-1.
* The `[code]` will be taken from the fhir-observation-code-1 valueset and for this API will always be `0002`.
* The `[base]` is the URL of the Spine endpoint. Note: Details of Spine endpoint addresses for test regions can be found on the Assurance Support Portal
* _Note:_ The mime-type can be specified to request either XML or JSON using another URL parameter `?_format=[mime-type]`, or a Content-Type HTTP header as per the FHIR specification.

Hence the request below would be used to request prescription exemptions for the patient with NHS Number 987543210:

```
GET https://spine-url/Observation?subject:Patient.identifier=https://fhir.nhs.uk/Id/nhs-number|987543210&code=https://fhir.nhs.uk/fhir-observation-code-1|0002
```

This would further need to be url-encoded to escape special characters:

```
GET https://spine-url/Observation?subject%3APatient.identifier=https%3A%2F%2Ffhir.nhs.uk%2FId%2Fnhs-number%7C1234567890&code=https%3A%2F%2Ffhir.nhs.uk%2Ffhir-observation-code-1%7C0002
```

#### XML Example 1 - Bundle Patient ####

```
<Observation xmlns="http://hl7.org/fhir"><id value="76e39290-d1aa-11e6-9598-0800200c9a66"/>
  <meta><profile value="https://fhir.nhs.uk/StructureDefinition/prescription-exemption-observation-1"/></meta><status value="final"/>
  <code>
    <coding>
      <system value="https://fhir.nhs.uk/fhir-observation-code-1"/>
      <code value="0002"/>
      <display value="Prescription Exemption Observation"/>
    </coding>
  </code>
  <subject>
    <reference value="Patient/9900002831"/>
    <display value="TAYLOR, Mary (Miss)"/>
  </subject>
  <effectivePeriod>
    <end value="2018-06-01"/>
  </effectivePeriod>
  <valueCodeableConcept>
      <coding>
        <system value="https://fhir.nhs.uk/prescription-exemptiontype-1"/>
          <code value="9006"/>
          <display value="has a valid medical exemption certificate - confirmed by source"/>
        </coding>
  </valueCodeableConcept>
</Observation>
<Observation xmlns="http://hl7.org/fhir"><id value="76e39290-d1aa-11e6-9598-0800200c9a65"/>
  <meta><profile value="https://fhir.nhs.uk/StructureDefinition/prescription-exemption-observation-1"/></meta><status value="final"/>
  <code>
    <coding>
      <system value="https://fhir.nhs.uk/fhir-observation-code-1"/>
      <code value="0002"/>
      <display value="Prescription Exemption Observation"/>
    </coding>
  </code>
  <subject>
    <reference value="Patient/9900002831"/>
    <display value="TAYLOR, Mary (Miss)"/>
  </subject>
  <effectivePeriod>
    <end value="2018-06-01"/>
  </effectivePeriod>
  <valueCodeableConcept>
      <coding>
        <system value="https://fhir.nhs.uk/prescription-exemptiontype-1"/>
          <code value="9002"/>
          <display value="Chargeable"/>
        </coding>
  </valueCodeableConcept>
</Observation>
```
