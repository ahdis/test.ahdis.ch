# Transformation CDA to FHIR and back

With the maps from the project [cda-fhir-maps](https://github.com/ahdis/cda-fhir-maps) CDA documents can be transformed into FHIR documents and back. This manual describes for which exchange formats the transformation can be used and how it is executed.

## Supported document types
The maps are intended for the transformation of documents, especially **eMedication documents**, in the context of the **Swiss EPR**. The scope is based on the eMedication case study; there is as yet no final mapping of data types, templates and documents.

The following document types are supported and examples of these are shown in these directories of the project [cda-fhir-maps](https://github.com/ahdis/cda-fhir-maps).

Document type | CDA examples | FHIR examples | Map CDA to FHIR | Map FHIR to CDA
-------- | -------- | -------- | -------- | --------
**Swiss EPR document** | input\cda-ch | input\ch-core | input\maps\CdaChToBundle.map | input\maps\BundleToCdaCh.map
**Medication Card document** | input\cda-ch-emed | input\ch-emed | input\maps\CdaChEmed MedicationCardDocumentToBundle.map | input\maps\BundleToCdaChEmed MedicationCardDocument.map
**Medication Prescription document** | input\cda-ch-emed | input\ch-emed | input\maps\CdaChEmed MedicationPrescriptionDocumentToBundle.map | input\maps\BundleToCdaChEmed MedicationPrescriptionDocument.map
**Medication Dispense document** | input\cda-ch-emed | input\ch-emed | input\maps\CdaChEmed MedicationDispenseDocumentToBundle.map | input\maps\BundleToCdaCh EmedMedicationDispenseDocument.map

***Table 1**: Document types with examples and maps*

### Specification

The example documents are structured according to the CDA/FHIR specifications for the exchange formats. The specification of the eMedication depends on the specification of the Swiss EPR exchange format.

Documents in the context of the Swiss EPR:

* Art-Decor [CDA-CH](https://art-decor.org/art-decor/decor-project--hl7chcda-)
   * [EPR Document (CDA)](https://art-decor.org/art-decor/decor-templates--hl7chcda-?section=templates&amp;id=2.16.756.5.30.1.1.10.1.9&amp;effectiveDate=2019-10-17T15:22:41&amp;language=en-US)
   
* FHIR Implementation Guide [CH Core](http://fhir.ch/ig/ch-core/index.html)
   * [EPR Document (FHIR)](https://build.fhir.org/ig/hl7ch/ch-core//StructureDefinition-ch-core-document-epr.html)

eMedication Documents:

* Art-Decor [CDA-CH-EMED eMedication](https://art-decor.org/art-decor/decor-project--cdachemed-)
   * [Medication Card document (CDA)](https://art-decor.org/art-decor/decor-templates--cdachemed-?section=templates&amp;id=2.16.756.5.30.1.1.10.1.3&amp;effectiveDate=2016-05-13T00:00:00&amp;language=en-US)
   * [Medication Prescription document (CDA)](https://art-decor.org/art-decor/decor-templates--cdachemed-?section=templates&amp;id=2.16.756.5.30.1.1.10.1.4&amp;effectiveDate=2016-05-21T00:00:00&amp;language=en-US)
   * [Medication Dispense document (CDA)](https://art-decor.org/art-decor/decor-templates--cdachemed-?section=templates&amp;id=2.16.756.5.30.1.1.10.1.5&amp;effectiveDate=2016-05-21T00:00:00&amp;language=en-US)   
   
* FHIR Implementation Guide [CH EMED](http://fhir.ch/ig/ch-emed/index.html)
   * [Medication Card document (FHIR)](http://fhir.ch/ig/ch-emed/StructureDefinition-ch-emed-document-medicationcard.html)
   * [Medication Prescription document (FHIR)](http://fhir.ch/ig/ch-emed/StructureDefinition-ch-emed-document-medicationprescription.html)
    * [Medication Dispense document (FHIR)](http://fhir.ch/ig/ch-emed/StructureDefinition-ch-emed-document-medicationdispense.html)

## Setup

To transform the documents from CDA to FHIR and back, you should prepare the following setup:

### Editor

* Download [Visual Studio Code (VSC)](https://code.visualstudio.com/docs/setup/setup-overview)
* Install a REST Client for Visual Studio Code:   
    Open VSC, press **Ctrl + Shift + X**, search for **rest-client**, then install it (humao.rest-client) 

### Java

* Download [Java](https://www.java.com/de/download/help/download_options.xml)

### GitHub Desktop (optional)

* Download [GitHub Desktop](https://desktop.github.com/)

### Project

* Clone the project [cda-fhir-maps](https://github.com/ahdis/cda-fhir-maps):
   * Option 1: GitHub Desktop
   * Option 2: VSC Terminal:   
    `git clone https://github.com/ahdis/cda-fhir-maps.git`

## Transformation eMedication documents

There are different mapping engines to perform the transformation. In the following two possibilities of Java FHIR mapping are described:

 - Micro-Service with matchbox (test.ahdis.ch/r4)
 - Java Validator

### Transformation with matchbox (test.ahdis.ch/r4)

1. Open the cloned project [cda-fhir-maps](https://github.com/ahdis/cda-fhir-maps) with Visual Studio Code and go to its main directory

2. To provide the CDA and CH Core implementation guides, the transaction in the file **initmatchbox.http** must be executed before the first transformation:
   * `@host = https://test.ahdis.ch/r4`
   * Click on **Send Request**
   * On the right side of your VSC should open a REST Client tab with the response `HTTP/1.1 201 Created`

3. Depending on the document to be transformed, open the corresponding http-file
   * CDA to FHIR:
      * Medication Card document: **cdatofhir_card.http**
      * Medication Prescription document: **cdatofhir_prescription.http**
      * Medication Dispense document: **cdatofhir_dispense.http**  
      
   * FHIR to CDA:
      * Medication Card document: **fhirtocda_card.http**
      * Medication Prescription document: **fhirtocda_prescription.http**
      * Medication Dispense document: **fhirtocda_dispense.http**      
     
4. Make sure that the correct server is specified in the file:   
    `@host = https://test.ahdis.ch/r4`   
    
5. To posts the required maps you have to do **Step 1-4** (do it in the right order)

6. Step 5 is finally the transformation itself. You can now perform this transformation step for as many documents of the selected type as you like. For this, adjust the input document to be transformed:   
    `< ./input/cda-ch-emed/2-7-MedicationCard.xml`

### Tranformation with Java Validator

1. Open the cloned project [cda-fhir-maps](https://github.com/ahdis/cda-fhir-maps) with Visual Studio Code and go to its main directory   

2. Download the latest Java Validator:   
    `wget https://storage.googleapis.com/ig-build/org.hl7.fhir.validator.jar -O org.hl7.fhir.validator.jar`   
    
3. Execute one of the following commands depending on the document:
    
   * CDA to FHIR:
      * Medication Card document:   
     `java -jar org.hl7.fhir.validator.jar -version 4.0.1 -ig cda-core-2.0-pr.tgz -ig ch.fhir.ig.ch-core#1.0.0 -transform http://ahdis.ch/ig/cda-fhir-maps/StructureMap/CdaChEmedMedicationCardDocumentToBundle -ig input/maps -log test.txt -output 2-7-Bundle.json input/cda-ch-emed/2-7-MedicationCard.xml`
      * Medication Prescription document:   
     `java -jar org.hl7.fhir.validator.jar -version 4.0.1 -ig cda-core-2.0-pr.tgz -ig ch.fhir.ig.ch-core#1.0.0 -transform http://ahdis.ch/ig/cda-fhir-maps/StructureMap/CdaChEmedMedicationPrescriptionDocumentToBundle -ig input/maps -log test.txt -output 2-6-Bundle.json input/cda-ch-emed/2-6-MedicationPrescription.xml`
      * Medication Dispense document:   
     `java -jar org.hl7.fhir.validator.jar -version 4.0.1 -ig cda-core-2.0-pr.tgz -ig ch.fhir.ig.ch-core#1.0.0 -transform http://ahdis.ch/ig/cda-fhir-maps/StructureMap/CdaChEmedMedicationDispenseDocumentToBundle -ig input/maps -log test.txt -output 2-4-Bundle.json input/cda-ch-emed/2-4-MedicationDispense.xml`   

4. You will find the transformed document and a log file in the main directory

5. For the transformation of **your own example**, adjust the following parameters in step 3:

   * Output as XML instead JSON:   
    change `-output 2-7-Bundle.json` to `-output 2-7-Bundle.xml`
   * Your own input file:   
    change `input/cda-ch-emed/2-7-MedicationCard.xml` to the path of your file

## Links for further informations

* [HL7 FHIR](http://www.hl7.org/fhir/)
* [FHIR Mapping Language (FML)](https://www.hl7.org/fhir/mapping-language.html)
* [FHIR Logical model for CDA](http://build.fhir.org/ig/HL7/cda-core-2.0/branches/master/index.html)
