# Validation of eMedication documents

This manual describes how eMedication documents can be validated.

## Specification

The eMedication exchange formats are defined as CDA documents on Art-Decor and as FHIR documents in a FHIR implementation guide. The specification of the eMedication depends on the specification of the Swiss EPR exchange format.

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
   * [Medication Treatment Plan document (CDA)](https://art-decor.org/art-decor/decor-templates--cdachemed-?section=templates&id=2.16.756.5.30.1.1.10.1.7&effectiveDate=2017-04-12T13:57:31&language=en-US)
   * [Pharmaceutical Advice document (CDA)](https://art-decor.org/art-decor/decor-templates--cdachemed-?section=templates&id=2.16.756.5.30.1.1.10.1.6&effectiveDate=2016-05-21T00:00:00&language=en-US)
   * [Medication List document (CDA)](https://art-decor.org/art-decor/decor-templates--cdachemed-?section=templates&id=2.16.756.5.30.1.1.10.1.13&effectiveDate=2018-01-22T15:17:26&language=en-US)
   
* FHIR Implementation Guide [CH EMED](http://fhir.ch/ig/ch-emed/index.html)
   * [Medication Card document (FHIR)](http://fhir.ch/ig/ch-emed/StructureDefinition-ch-emed-document-medicationcard.html)
   * [Medication Prescription document (FHIR)](http://fhir.ch/ig/ch-emed/StructureDefinition-ch-emed-document-medicationprescription.html)
    * [Medication Dispense document (FHIR)](http://fhir.ch/ig/ch-emed/StructureDefinition-ch-emed-document-medicationdispense.html)
    * [Medication Treatment Plan document (FHIR)](http://fhir.ch/ig/ch-emed/StructureDefinition-ch-emed-document-medicationtreatmentplan.html)
    * [Pharmaceutical Advice document (FHIR)](http://fhir.ch/ig/ch-emed/StructureDefinition-ch-emed-document-pharmaceuticaladvice.html)
    * [Medication List document (FHIR)](http://fhir.ch/ig/ch-emed/StructureDefinition-ch-emed-document-medicationlist.html)

## Setup

To validate the eMedication documents, you should prepare the following setup:

### Editor

* Download [Visual Studio Code (VSC)](https://code.visualstudio.com/docs/setup/setup-overview)
* Install a **REST Client** for Visual Studio Code:   
  Open VSC, press **Ctrl + Shift + X**, search for **rest-client**, then install it (humao.rest-client) 
* Install the **FHIR tools** extension:   
  Open VSC, press **Ctrl + Shift + X**, search for **FHIR**, then install it (yannick-lagger.vscode-fhir-tools)

### Java

* Download [Java](https://www.java.com/de/download/help/download_options.xml)

### GitHub Desktop (optional)

* Download [GitHub Desktop](https://desktop.github.com/)

## Validation

### Validation FHIR documents

### Validation CDA documents
