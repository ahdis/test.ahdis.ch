# Mobile Access Gateway

In the following individual steps of a use case are described, which take place in the context of the PoC for the connection of Documedis to the EPR via the Mobile Access Gateway. Figure 1 shows on the left side the app, in this case the Documedis client with the respective actors and on the right side the Mobile Access Gateway with the respective actors. The defined transactions are shown in the middle.   

In the following description the actors on the Documedis side and the transactions are considered. In the use case it is assumed that the patient has already opened an EPR.

![Headers](https://github.com/ahdis/test.ahdis.ch/blob/master/images/MobileAccessGateway.png)
*Fig. 1: Mobile Access Gateway*

The examples for the transactions (http requests) can be tested with the following server:   
@host = http://test.ahdis.ch/fhir



## How to get the MPI-PID of the patient

### Mobile Patient Identifier Crossreference Query [ITI-83]

A mobile app (Documedis) wants to access (read or write) documents, which requires the **MPI-PID** of the patient.   

The Mobile Patient Identifier Crossreference Query is used by an app in the Swiss EPR to query with the local identifier the MPI and get the corresponding MPI-PID and the EPR-SPID identifier for the patient. 

**Actor:** PMIR Patient Identifier Crossreference Consumer    
**Role:** Queries the Patient Identifier Crossreference Manager for the MPI-PID and EPR-SPID.

**Message Example**:   
```
### ITI-83 pixm mock call
GET {{host}}/Patient/$ihe-pix?sourceIdentifier=urn:oid:1.2.3.4|0815&targetSystem=urn:oid:1.2.3.4.6 HTTP/1.1
Accept: application/fhir+json
Content-Type: application/fhir+json
```



## How to write a document

### MHD: Provide Document Bundle [ITI-65]

This method is invoked when the Document Source (Documedis) needs to **submit one or more documents** to a Document Recipient (Mobile Access Gateway).

In the Swiss EPR the transaction is used by the MHD Document Source to store documents in the EPR. 

**Actor:** MHD Document Source   
**Role:** Sends documents and metadata to the Document Recipient

**Message Example**:   
```
### ITI-65 Provide Document Bundle mock call
POST {{host}} HTTP/1.1
Accept: application/fhir+json
Content-Type: application/fhir+json

< ./src/test/resources/mhdch.json
```



## How to find a document

### MHD: Find Document Manifests [ITI-66]

**Actor:** MHD Document Consumer

**Message Example**:   
```
### ITI-66 Find Document Manifests
GET {{host}}/DocumentManifest?status=current&patient.identifier=urn:oid:2.999|11111111 HTTP/1.1
Accept: application/fhir+json
Content-Type: application/fhir+json
```

### MHD: Find Document References [ITI-67]

**Actor:** MHD Document Consumer

**Message Example**:   
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



## How to retrieve a document

### MHD: Retrieve Document [ITI-68]

**Actor:** MHD Document Consumer

**Message Example**:   
```
### ITI-68 Retrieve Document call
GET {{host}}/xdsretrieve?uniqueId=7fba239a-f518-11e9-802a-5aa538984bd8&repositoryUniqueId=2.999.756.42.1
Accept: application/fhir+json
```
