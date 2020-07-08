# Documedis Integration for the EPR

In the following two use cases are described, which illustrate the integration of Documedis for the EPR. 
Based on these use cases, the **transformation of documents** from FHIR to CDA and back and the transactions between Documedis and the **Mobile Access Gateway** are explained in more detail.

In the use cases it is assumed that the patient has already opened an EPR.


## Use Case 1 (read)

triaPHARM calls up the EPR of a patient from the CARA community via Documedis. Documedis displays a current overview of the relevant documents and a consolidated view of the medication.

- 1.1: triaPHARM: Transfer of data and access to Documedis
- 1.2: Documedis authenticates the HCP with HIN
- 1.3: **Documedis retrieves the documents from CARA/PMP using the Mobile Access Gateway**
- 1.4: Documedis displays the documents in the GUI
- 1.5: **Documedis converts CDA-CH-EMED documents into FHIR-CH-EMED documents**
- 1.6: Documedis calls the Analyzer with these documents
- 1.7: Display the consolidated medication from the Analyzer in the GUI

### 1.3: Documedis retrieves the documents from CARA/PMP using the Mobile Access Gateway

#### Mobile Patient Identifier Crossreference Query [ITI-83]

To get access for Documedis for reading documents, the MPI-PID of the patient is required.    
The transaction **Mobile Patient Identifier Crossreference Query [ITI-83]** is used by Documedis as **Patient Identifier Crossreference Consumer** to query with the local identifier the MPI and get the corresponding MPI-PID and the EPR-SPID identifier for the patient.

ITI-83 PIXm Request for MPI-PID:   
```
GET http://test.ahdis.ch/fhir/Patient/$ihe-pix?sourceIdentifier=urn:oid:1.2.3.4|0815&targetSystem=urn:oid:1.2.3.4.6 HTTP/1.1
Accept: application/fhir+json
```

ITI-83 PIXm Request for MPI-PID and EPR-SPID:   
```
GET http://test.ahdis.ch/fhir/Patient/$ihe-pix?sourceIdentifier=urn:oid:1.2.3.4|0815&targetSystem=urn:oid:1.2.3.4.6&targetSystem=urn:oid:2.16.756.5.30.1.127.3.10.3 HTTP/1.1
Accept: application/fhir+json
```

- 'sourceIdentifier=' (Documedis): assigning authority oid = 1.2.3.4, local patient identifier = 0815 
- 'targetSystem=' (Community): assigning authority oid = 1.2.3.4.6 (MPI-PID), request for EPR-SPID = urn:oid:2.16.756.5.30.1.127.3.10.3 

ITI-83 PIXm Response:
```
{
  "resourceType": "Parameters",
  "parameter": [
    {
      "name": "targetIdentifier",
      "valueIdentifier": {
        "system": "urn:oid:1.2.3.4.6",
        "value": "value of MPI-PID"
      }
    },
    {
      "name": "targetIdentifier",
      "valueIdentifier": {
        "system": "urn:oid:2.16.756.5.30.1.127.3.10.3",
        "value": "value of EPR-SPID"
      }
    }
  ]
}
```

See example above [here](http://build.fhir.org/ig/ehealthsuisse/ch-epr-mhealth/Parameters-ParametersPmirCrossreferenceQuery.json.html).

#### MHD: Find Document References [ITI-67]

The transaction **MHD: Find Document References [ITI-67]** is used by Documedis as **MHD Document Consumer** to find documents which are stored in the EPR of the patient.

ITI-67 Find Document References PMP Request:     
```
GET http://test.ahdis.ch/fhir/DocumentReference?status=current&patient.identifier=urn:oid:2.999.756.42.2|12345678 HTTP/1.1
Accept: application/fhir+json
```   

- 'status=' find all DocumentReferences with the requested status
- 'patient.identifier=' find all DocumentReferences of the patient with this identifier (system|value)

ITI-67 Find Document References PMP Response:     
```
{
  "resourceType": "Bundle",
  "id": "b8fe9041-b42f-4873-839a-2043278f16e8",
  "meta": {
    "lastUpdated": "2020-06-30T07:49:29.476+00:00"
  },
  "type": "searchset",
  "total": 1,
  "link": [
    {
      "relation": "self",
      "url": "http://test.ahdis.ch/fhir/DocumentReference?patient.identifier=urn%3Aoid%3A2.999.756.42.2%7C12345678&status=current"
    }
  ],
  "entry": [
    {
      "fullUrl": "http://test.ahdis.ch/fhir/DocumentReference/999",
      "resource": {
        "resourceType": "DocumentReference",
        "id": "999",
        .
        .
        .
        "content" : [
          {
            "attachment" : {
              "contentType" : "text/xml",
              "language" : "de-CH",
              "url" : "http://test.fhir.ch/camel/xdsretrieve?uniqueId=1.3.6.1.4.1.12559.11.13.2.1.2951&repositoryUniqueId=1.1.4567332.1.2",
              "size" : 309219,
              "hash" : "YzY3ZDg2MTVmNjA2MzlkMTdkMzM2NGIwNDRhYzkxMzk2ZmQ4NDllOQ==",
              "creation" : "2020-06-29T11:58:00+00:00"
            },
            "format" : {
              "system" : "urn:oid:1.3.6.1.4.1.19376.1.2.3",
              "code" : "urn:ihe:pharm:pml:2013",
              "display" : "Community Medication List"
            }
          }
        ],
        .
        .
        .
      }
    }
  ]   
}
```

See full example of [DocumentReference](http://build.fhir.org/ig/ehealthsuisse/ch-epr-mhealth/DocumentReference-2-7-DocRefMedicationCard.json.html).

#### MHD: Retrieve Document [ITI-68]

The transaction **MHD: Retrieve Document [ITI-68]** is used by Documedis as **MHD Document Consumer** to retrieve documents which are stored in the EPR of the patient.

ITI-68 Retrieve Document Request:   
```
GET http://test.fhir.ch/camel/xdsretrieve?uniqueId=1.3.6.1.4.1.12559.11.13.2.1.2951&repositoryUniqueId=1.1.4567332.1.2
Accept: application/fhir+json
```

- The URL for the GET Request can be found in the Response of the transaction 'MHD: Find Document References [ITI-67]' in the element DocumentReference.content.attachment.url.


### 1.5: Documedis converts CDA-CH-EMED documents into FHIR-CH-EMED documents

To transform the Medication Card document from CDA to FHIR use this [transformation service](https://github.com/ahdis/test.ahdis.ch/blob/master/Transformation_FHIR-CDA.md#transformation-documents-from-cda-to-fhir-and-back-as-a-service).

Transformation of a Medication Card document from CDA to FHIR (json):
```
POST https://test.ahdis.ch/r4/StructureMap/$transform?source=http://fhir.ch/ig/cda-fhir-maps/StructureMap/CdaChEmedMedicationCardDocumentToBundle
Accept: application/fhir+json;fhirVersion=4.0
Content-Type: application/fhir+xml;fhirVersion=4.0

< ./MedicationCard.xml
```

- 'source=' references to the required StructureMap ([available Maps](https://github.com/ahdis/test.ahdis.ch/blob/master/Transformation_FHIR-CDA.md#post-requests))


## Use Case 2 (write)

triaPHARM generates a Mediplan via Documedis and this is stored as a Medication Card document for a patient in the CARA community in EPR/PMP.

- 2.1: triaPHARM: Transfer of data and access to Documedis
- 2.2: triaPHARM generates a Mediplan (CHMED20AF) via Documedis
- 2.3: **Documedis converts this generated FHIR-CH-EMED document into a CDA-CH-EMED document**
- 2.4: Documedis authenticates the HCP with HIN
- 2.5: **Documedis sends the Medication Card document to CARA/PMP using the Mobile Access Gateway**

### 2.3: Documedis converts this generated FHIR-CH-EMED document into a CDA-CH-EMED document

To transform the Medication Card document from FHIR to CDA use this [transformation service](https://github.com/ahdis/test.ahdis.ch/blob/master/Transformation_FHIR-CDA.md#transformation-documents-from-cda-to-fhir-and-back-as-a-service).

Transformation of a Medication Card document from FHIR (json) to CDA:
```
POST https://test.ahdis.ch/r4/StructureMap/$transform?source=http://fhir.ch/ig/cda-fhir-maps/StructureMap/BundleToCdaChEmedMedicationCardDocument
Accept: application/fhir+xml;fhirVersion=4.0
Content-Type: application/fhir+json;fhirVersion=4.0

< ./MedicationCard.json
```

- 'source=' references to the required StructureMap ([available Maps](https://github.com/ahdis/test.ahdis.ch/blob/master/Transformation_FHIR-CDA.md#post-requests))

### 2.5: Documedis sends the Medication Card document to CARA/PMP using the Mobile Access Gateway

#### Mobile Patient Identifier Crossreference Query [ITI-83]

To get access for Documedis for writing documents, the MPI-PID of the patient is required.   
The transaction **Mobile Patient Identifier Crossreference Query [ITI-83]** is used by Documedis as **Patient Identifier Crossreference Consumer** to query with the local identifier the MPI and get the corresponding MPI-PID and the EPR-SPID identifier for the patient.

ITI-83 PIXm Request for MPI-PID:   
```
GET http://test.ahdis.ch/fhir/Patient/$ihe-pix?sourceIdentifier=urn:oid:1.2.3.4|0815&targetSystem=urn:oid:1.2.3.4.6 HTTP/1.1
Accept: application/fhir+json
```

ITI-83 PIXm Request for MPI-PID and EPR-SPID:   
```
GET http://test.ahdis.ch/fhir/Patient/$ihe-pix?sourceIdentifier=urn:oid:1.2.3.4|0815&targetSystem=urn:oid:1.2.3.4.6&targetSystem=urn:oid:2.16.756.5.30.1.127.3.10.3 HTTP/1.1
Accept: application/fhir+json
```

- 'sourceIdentifier=' (Documedis): assigning authority oid = 1.2.3.4, local patient identifier = 0815 
- 'targetSystem=' (Community): assigning authority oid = 1.2.3.4.6 (MPI-PID), request for EPR-SPID = urn:oid:2.16.756.5.30.1.127.3.10.3 

ITI-83 PIXm Response:
```
{
  "resourceType": "Parameters",
  "parameter": [
    {
      "name": "targetIdentifier",
      "valueIdentifier": {
        "system": "urn:oid:1.2.3.4.6",
        "value": "value of MPI-PID"
      }
    },
    {
      "name": "targetIdentifier",
      "valueIdentifier": {
        "system": "urn:oid:2.16.756.5.30.1.127.3.10.3",
        "value": "value of EPR-SPID"
      }
    }
  ]
}
```

See example above [here](http://build.fhir.org/ig/ehealthsuisse/ch-epr-mhealth/Parameters-ParametersPmirCrossreferenceQuery.json.html).

#### MHD: Provide Document Bundle [ITI-65]

The transaction **MHD: Provide Document Bundle [ITI-65]** is used by Documedis as **MHD Document Source** to submit one (or more) documents to the Mobile Access Gateway and thus store the document in the EPR.

ITI-65 Provide Document Bundle Request:   
```
POST http://test.ahdis.ch/fhir HTTP/1.1
Accept: application/fhir+json
Content-Type: application/fhir+json

< ./src/test/resources/mhdch.json
```

- see [Example of MHD Provide Document Bundle for MedicationCard](http://build.fhir.org/ig/ehealthsuisse/ch-epr-mhealth/Bundle-2-7-BundleProvideDocument.json.html)


## Overview

Figure 1 shows an overview of the involved profiles, actors and transactions. On the left side the app, in this case the **Documedis** client with the respective actors, is shown and on the right side the **Mobile Access Gateway** with the respective actors is represented. The defined **transactions** are shown in the middle.   

![Headers](https://github.com/ahdis/test.ahdis.ch/blob/master/images/OverviewNationalExtension.png)   
*Fig. 1: Profiles, actors and transactions covered in the national extension and profile for mHealth*


## Sources
- [Draft specification of national extension and profile for mHealth](https://groups.google.com/u/1/g/epd_projectathon/c/XsWT8Rnbfuw)
- [Sequence Diagrams get document](https://github.com/ahdis/ch-epr-seqdiag/tree/mhealth2020)
- [Implementation Guide CH EPR mHealth (CI build)](http://build.fhir.org/ig/ehealthsuisse/ch-epr-mhealth/)
- [Patient Master Identity Registry (PMIR)](https://www.ihe.net/uploadedFiles/Documents/ITI/IHE_ITI_Suppl_PMIR.pdf)
- [Patient Identifier Cross-reference for Mobile (PIXm)](https://www.ihe.net/uploadedFiles/Documents/ITI/IHE_ITI_Suppl_PIXm.pdf)
- [Mobile access to Health Documents (MHD) With XDS on FHIR ](https://www.ihe.net/uploadedFiles/Documents/ITI/IHE_ITI_Suppl_MHD.pdf)
