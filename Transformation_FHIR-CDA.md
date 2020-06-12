# Transformation documents from CDA to FHIR and back as a service

The transformation of documents from CDA to FHIR and back is offered as a micro service. The basis of this service are the maps from the project [cda-fhir-maps](https://github.com/hl7ch/cda-fhir-maps) describing the mapping between the CDA and FHIR elements and a hapi fhir spring boot server ([matchbox](https://github.com/ahdis/matchbox)) supporting the [$transform operation for StructureMaps](http://www.hl7.org/fhir/structuremap-operation-transform.html).   
This manual describes which document types are supported and how the service can be used.

## Supported document types
The maps are intended for the transformation of documents, especially **eMedication documents**, in the context of the **Swiss EPR**.

The transformation of following document types are supported:

* Medication Card document
* Medication Prescription document
* Medication Dispense document
* Swiss EPR document 

*Note: The scope is based on the eMedication case study; there is as yet no final mapping of data types, templates and documents.*

## Running transformation

Two possibilities are shown here, how the transformation can be executed:
1. Transformation with Visual Studio Code and a REST Client Extension
2. Transformation with Postman

The transformation process is the same for both variants. The $transform operation takes input content, applies a structure map transform, and then returns the output.

* The matchbox (https://test.ahdis.ch/r4) provides the maps needed and supports **$transform** for conversion between CDA and FHIR and back
* Apply the correct **structure map** (source=) to convert the required document type from one exchange format to another (see [POST requests](#POST requests))
* Choose your **input document**: CDA (xml) or FHIR (xml/json)
* Get your converted **output document**: CDA (xml) or FHIR (xml/json)

### POST requests

* CDA to FHIR

   * Medication Card document:   
   https://test.ahdis.ch/r4/StructureMap/$transform?source=http://fhir.ch/ig/cda-fhir-maps/StructureMap/CdaChEmedMedicationCardDocumentToBundle
   
   * Medication Prescription document:   
   https://test.ahdis.ch/r4/StructureMap/$transform?source=http://fhir.ch/ig/cda-fhir-maps/StructureMap/CdaChEmedMedicationPrescriptionDocumentToBundle
   
   * Medication Dispense document:   
   https://test.ahdis.ch/r4/StructureMap/$transform?source=http://fhir.ch/ig/cda-fhir-maps/StructureMap/CdaChEmedMedicationDispenseDocumentToBundle
   
   * Swiss EPR document:   
   https://test.ahdis.ch/r4/StructureMap/$transform?source=http://fhir.ch/ig/cda-fhir-maps/StructureMap/CdaChToBundle
   
   
* FHIR to CDA

   * Medication Card document:   
   https://test.ahdis.ch/r4/StructureMap/$transform?source=http://fhir.ch/ig/cda-fhir-maps/StructureMap/BundleToCdaChEmedMedicationCardDocument
   
   * Medication Prescription document:   
   https://test.ahdis.ch/r4/StructureMap/$transform?source=http://fhir.ch/ig/cda-fhir-maps/StructureMap/BundleToCdaChEmedMedicationPrescriptionDocument
   
   * Medication Dispense document:   
   https://test.ahdis.ch/r4/StructureMap/$transform?source=http://fhir.ch/ig/cda-fhir-maps/StructureMap/BundleToCdaChEmedMedicationDispenseDocument
   
   * Swiss EPR document:   
   https://test.ahdis.ch/r4/StructureMap/$transform?source=http://fhir.ch/ig/cda-fhir-maps/StructureMap/BundleToCdaCh


### Transformation with Visual Studio Code and a REST Client Extension
![POST request](https://github.com/ahdis/test.ahdis.ch/blob/master/images/Transformation-POST-VSCode.png)
*Fig.: POST request to transform a Medication Card document from CDA to FHIR in Visual Studio Code*


### Transformation with Postman

#### Input
The POST request has the document, the content to transform, in the body.

![POST request](https://github.com/ahdis/test.ahdis.ch/blob/master/images/Transformation-POST.png)
*Fig.: POST request to transform a Medication Card document from CDA to FHIR in Postman*

#### File format (xml/json)
Make sure to set the Content-/Accept-Type to either `application/fhir+json;fhirVersion=4.0` or `application/fhir+json;fhirVersion=4.0` accordingly.

![Headers](https://github.com/ahdis/test.ahdis.ch/blob/master/images/Transformation-Headers.png)
*Fig.: Set Headers in Postman*

## Create your own maps
To create additional own maps and perform the transformation locally, see the [README](https://github.com/hl7ch/cda-fhir-maps#implementation-guide-cda-fhir-maps) of the project [cda-fhir-maps](https://github.com/hl7ch/cda-fhir-maps).

## Links for further informations

* [HL7 FHIR](http://www.hl7.org/fhir/)
* [FHIR Mapping Language (FML)](https://www.hl7.org/fhir/mapping-language.html)
* [FHIR Logical model for CDA](http://build.fhir.org/ig/HL7/cda-core-2.0/branches/master/index.html)
