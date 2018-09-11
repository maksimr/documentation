# Overview

FHIR defines [terminology service](https://www.hl7.org/fhir/terminology-service.html#4.6) to simplify usage of terminologies.

> A terminology service is a service that lets healthcare applications make use of codes and value sets without having to become experts in the fine details of code system, value set and concept map resources, and the underlying code systems and terminological principles.

**Code System** is  a set of codes with meanings \(also known as enumeration, dictionary, terminology, classification, and/or ontology; you probably know ICD-10, RxNorm, SNOMED, LOINC\).

In FHIR many resource elements are represented as coded values. For example to encode Lab Tests in Observation resource you may be want to use LOINC or RxNorm for medication prescriptions. FHIR has special data types for coded values:

* [**code**](https://www.hl7.org/fhir/datatypes.html#code) - code as string strictly bound to predefined ValueSet \(usually used for fixed codes defined by standard like Encounter.status or Patient.gender\)
* [**Coding**](https://www.hl7.org/fhir/datatypes.html#Coding) - complex type with system & code attributes; system refers to specific Code System
* [**CodeableConcept**](https://www.hl7.org/fhir/datatypes.html#codeableconcept) - complex type, which may contain many Codings - so it can be coded using multiple Code Systems \(for example Lab Tests can be coded by proprietary internal code system and by LOINC\)

To specify what codes can be assigned to specific element FHIR defines **binding** of element definition to **ValueSet**. **ValueSet** selects a set of codes from those defined by one or more code systems. 

In aidbox all terminology services are built around non-FHIR  **Concept** resource type. Concept resource behaves like other FHIR resources - you can CRUD & Search it. Concept structure essentially follows structure of Coding data type with some additional attributes.

Terminology service can be logically spit into two parts:

* terminology service for your app to lookup and validate existing codes
* terminology management - to upload new code systems and create new value sets
