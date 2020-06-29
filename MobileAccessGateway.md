# Documedis Integration with Mobile Access Gateway

In the following two use cases are described, which take place in the context of the PoC for the connection of Documedis to the EPR via the Mobile Access Gateway. The first use case describes how a Medication Card document can be write into the EPR. The second use case describes how EPR eMedication documents can be retrieved.

Figure 1 shows an overview of the involved profiles, actors and transactions. On the left side the app, in this case the Documedis client with the respective actors, is shown and on the right side the Mobile Access Gateway with the respective actors is represented. The defined transactions are shown in the middle.   

![Headers](https://github.com/ahdis/test.ahdis.ch/blob/master/images/MobileAccessGateway.png)   
*Fig. 1: Mobile Access Gateway*

In the following description the actors on the Documedis side and the transactions are considered. In the use cases it is assumed that the patient has already opened an EPR.

*The examples for the transactions (http requests) can be tested with the following server:*   
*@host = http://test.ahdis.ch/fhir*


## Use Case 1: How to write the Medication Card document into the EPR
Use Case 1 shows how Documedis writes a Medication Card document (CHMED20AF) as an FHIR document in JSON format, in the EPR as a CDA document. The following substeps are needed for this:

1.1 Transform the FHIR document into the CDA document   
1.2 Write the Medication Card document in the patient's EPR   

### 1.1 Transform the FHIR document into the CDA document

To transform the Medication Card document from FHIR to CDA use this [transformation service](https://github.com/ahdis/test.ahdis.ch/blob/master/Transformation_FHIR-CDA.md#transformation-documents-from-cda-to-fhir-and-back-as-a-service):

```
POST https://test.ahdis.ch/r4/StructureMap/$transform?source=http://fhir.ch/ig/cda-fhir-maps/StructureMap/BundleToCdaChEmedMedicationCardDocument
Accept: application/fhir+xml;fhirVersion=4.0
Content-Type: application/fhir+json;fhirVersion=4.0

< ./MedicationCard.json
```

### 1.2 Write the Medication Card document in the patient's EPR

To get access for Documedis for writing documents, the MPI-PID of the patient is required. 
The transaction **Mobile Patient Identifier Crossreference Query [ITI-83]** is used by Documedis as **Patient Identifier Crossreference Consumer** to query with the local identifier the MPI and get the corresponding MPI-PID and the EPR-SPID identifier for the patient.

Message Example:   
```
### ITI-83 pixm mock call
GET {{host}}/Patient/$ihe-pix?sourceIdentifier=urn:oid:1.2.3.4|0815&targetSystem=urn:oid:1.2.3.4.6 HTTP/1.1
Accept: application/fhir+json
Content-Type: application/fhir+json
```

The transaction **MHD: Provide Document Bundle [ITI-65]** is used by Documedis as **MHD Document Source** to submit one (or more) documents to the Mobile Access Gateway and thus store the document in the EPR.

Message Example:   
```
### ITI-65 Provide Document Bundle mock call
POST {{host}} HTTP/1.1
Accept: application/fhir+json
Content-Type: application/fhir+json

< ./src/test/resources/mhdch.json
```

## Use Case 2: How to retrieve eMedication documents from the EPR
Use Case 2 shows how Documedis retrieves eMedication documents as CDA documents from the EPR. If the retrieved document is a Medication Card document, it will be shown, how the CDA document is transformed to a FHIR document (CHMED20AF). The following substeps are needed for this:

2.1 Retrieve the eMedication documents from the EPR   
2.2 Transform the CDA document into the FHIR document   

### 2.1 Retrieve the eMedication documents from the EPR   

To get access for Documedis for reading documents, the MPI-PID of the patient is required. 
The transaction **Mobile Patient Identifier Crossreference Query [ITI-83]** is used by Documedis as **Patient Identifier Crossreference Consumer** to query with the local identifier the MPI and get the corresponding MPI-PID and the EPR-SPID identifier for the patient.

Message Example:   
```
### ITI-83 pixm mock call
GET {{host}}/Patient/$ihe-pix?sourceIdentifier=urn:oid:1.2.3.4|0815&targetSystem=urn:oid:1.2.3.4.6 HTTP/1.1
Accept: application/fhir+json
Content-Type: application/fhir+json
```

The transaction **MHD: Find Document Manifests [ITI-66]** or **MHD: Find Document References [ITI-67]** is used by Documedis as **MHD Document Consumer** to find documents which are stored in the EPR of the patient.

Message Example for Document Manifest:   
```
### ITI-66 Find Document Manifests
GET {{host}}/DocumentManifest?status=current&patient.identifier=urn:oid:2.999|11111111 HTTP/1.1
Accept: application/fhir+json
Content-Type: application/fhir+json
```

Message Examples for Document References:     
```
### ITI-67 Find Document References PMP
GET {{host}}/DocumentReference?status=current&patient.identifier=urn:oid:2.999.756.42.2|12345678 HTTP/1.1
Accept: application/fhir+json
Content-Type: application/fhir+json
```   

```
### ITI-67 Find Document References XDSTools7 ahdis
GET {{host}}/DocumentReference?status=current&patient.identifier=urn:oid:1.3.6.1.4.1.21367.13.20.1000|IHERED-2595 HTTP/1.1
Accept: application/fhir+json
Content-Type: application/fhir+json
```

The transaction **MHD: Retrieve Document [ITI-68]** is used by Documedis as **MHD Document Consumer** to retrieve documents which are stored in the EPR of the patient.

Message Example:   
```
### ITI-68 Retrieve Document call
GET {{host}}/xdsretrieve?uniqueId=7fba239a-f518-11e9-802a-5aa538984bd8&repositoryUniqueId=2.999.756.42.1
Accept: application/fhir+json
```

### 2.2 Transform the CDA document into the FHIR document   
To transform the Medication Card document from CDA to FHIR use this [transformation service](https://github.com/ahdis/test.ahdis.ch/blob/master/Transformation_FHIR-CDA.md#transformation-documents-from-cda-to-fhir-and-back-as-a-service):

```
POST https://test.ahdis.ch/r4/StructureMap/$transform?source=http://fhir.ch/ig/cda-fhir-maps/StructureMap/CdaChEmedMedicationCardDocumentToBundle
Accept: application/fhir+json;fhirVersion=4.0
Content-Type: application/fhir+xml;fhirVersion=4.0

< ./MedicationCard.xml
```

## Sources
- [Draft specification of national extension and profile for mHealth](https://groups.google.com/u/1/g/epd_projectathon/c/XsWT8Rnbfuw)
- [Sequence Diagrams get document](https://github.com/ahdis/ch-epr-seqdiag/tree/mhealth2020)
- [Implementation Guide CH EPR mHealth (CI build)](http://build.fhir.org/ig/ehealthsuisse/ch-epr-mhealth/)
- [Patient Master Identity Registry (PMIR)](https://www.ihe.net/uploadedFiles/Documents/ITI/IHE_ITI_Suppl_PMIR.pdf)
- [Patient Identifier Cross-reference for Mobile (PIXm)](https://www.ihe.net/uploadedFiles/Documents/ITI/IHE_ITI_Suppl_PIXm.pdf)
- [Mobile access to Health Documents (MHD) With XDS on FHIR ](https://www.ihe.net/uploadedFiles/Documents/ITI/IHE_ITI_Suppl_MHD.pdf)