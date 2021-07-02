# Validation of FHIR resources as a service

The validation of FHIR resources is offered as a microservice. The basis of this service are a hapi fhir spring boot server ([matchbox](https://github.com/ahdis/matchbox)) supporting the [$validate operation on Resources](https://www.hl7.org/fhir/resource-operation-validate.html) and the defined profiles of the [Swiss implementation guides](http://fhir.ch/).
This manual describes which FHIR implementation guides are supported and how the service can be used. 

## Supported FHIR implementation guides
This microservice supports validation against all profiles (StructureDefinitions) from the following FHIR implementation guides:

* [CH Core (R4)](http://fhir.ch/ig/ch-core/index.html) (FHIR Core profiles for Switzerland, incl. in the context of EPR)   
* [CH EMED (R4)](http://fhir.ch/ig/ch-emed/index.html) (FHIR exchange format for eMedication in Switzerland)
* [CHMED20AF (R4)](http://chmed20af.emediplan.ch/) (FHIR based definition for the eMediplan CHMED16A)
* [CH VACD (R4)](http://fhir.ch/ig/ch-vacd/index.html) (FHIR exchange format for immunization and vaccination information)
* [CH CRL (R4)](http://fhir.ch/ig/ch-crl/index.html) (FHIR exchange format for the cancer registration)
* [CH EMS (R4)](http://fhir.ch/ig/ch-ems/index.html) (FHIR exchange format for the emergency medical service protocol) 


## Running validation

Two possibilities are shown here, how the validation can be executed:
1. [Validation with Visual Studio Code and a REST Client Extension](#validation-with-visual-studio-code-and-a-rest-client-extension)
2. [Validation with Postman](#validation-with-postman)

The validation process is the same for both variants. The $validate operation checks whether the content of a resource would be acceptable. The validation can be performed generally (FHIR Core specification) or against a defined profile.

* The matchbox (https://test.ahdis.ch/r4) provides the above mentioned implementation guides and supports a system wide **$validate** operation (with profile as query parameter)
* If desired apply a **profile** to validate against (see [POST requests](#post-requests))
* Choose your FHIR **resource** (xml/json)
* Get a response on a validation operation ([Operation Outcome](https://www.hl7.org/fhir/operationoutcome.html)) to provide **information about the outcome** (error, warning and information messages)

### POST requests

* Validate a resource with no profile:   
   https://test.ahdis.ch/r4/$validate

* Validate a resource with a profile (e.g. CH Core Patient):   
   https://test.ahdis.ch/r4/$validate?profile=http://fhir.ch/ig/ch-core/StructureDefinition/ch-core-patient


### Validation with Visual Studio Code and a REST Client Extension
![POST request](https://github.com/ahdis/test.ahdis.ch/blob/master/images/Validation-noProfile-VSCode.png)
*Fig. 1: POST request to validate a resource in Visual Studio Code*

```
POST https://test.ahdis.ch/r4/$validate HTTP/1.1
Content-Type: application/xml

<Patient xmlns="http://hl7.org/fhir">
  <id value="MaxMuster"/>
  .
  .
  .
</Patient>
```

![POST request](https://github.com/ahdis/test.ahdis.ch/blob/master/images/Validation-Profile-VSCode.png)
*Fig. 2: POST request to validate a resource against the profile "CH Core Patient" in Visual Studio Code*

```
POST https://test.ahdis.ch/r4/$validate?profile=http://fhir.ch/ig/ch-core/StructureDefinition/ch-core-patient HTTP/1.1
Content-Type: application/xml

<Patient xmlns="http://hl7.org/fhir">
  <id value="MaxMuster"/>
  .
  .
  .
</Patient>
```

### Validation with Postman

#### Resource
The POST request has the resource to validate in the body.

![POST request](https://github.com/ahdis/test.ahdis.ch/blob/master/images/Validation-noProfile.png)
*Fig. 3: POST request to validate a resource in Postman*

![POST request](https://github.com/ahdis/test.ahdis.ch/blob/master/images/Validation-Profile.png)
*Fig. 4: POST request to validate a resource against the profile "CH Core Patient" in Postman*

#### File format (xml/json)
Make sure to set the Content-/Accept-Type to either `application/fhir+json;fhirVersion=4.0` or `application/fhir+json;fhirVersion=4.0` accordingly.

![Headers](https://github.com/ahdis/test.ahdis.ch/blob/master/images/Transformation-Headers.png)
*Fig. 5: Set Headers in Postman*
