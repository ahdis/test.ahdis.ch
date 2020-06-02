# Validation of FHIR resources

The validationof FHIR resources is offered as a micro service. The basis of this service are a hapi fhir spring boot server ([matchbox](https://github.com/ahdis/matchbox)) supporting the [$validate operation on Resources](https://www.hl7.org/fhir/resource-operation-validate.html) and the definded profiles of the [Swiss implementation guides](http://fhir.ch/).
This manual describes which FHIR implementation guides are supported and how the serive can be used. 

## Supported FHIR implementation guides
This micro service supports validation against all profiles (SturctureDefinitions) from the following FHIR implementation guides:

* [CH Core (R4)](http://fhir.ch/ig/ch-core/index.html) (Core FHIR profiles for Switzerland)   
* [CH EMED (R4)](http://fhir.ch/ig/ch-emed/index.html) (eMedication exchange format)
* [CHMED20AF (R4)](http://chmed20af.emediplan.ch/) (FHIR based definition for the eMediplan CHMED16A)
* [CH CRL (R4)](http://fhir.ch/ig/ch-crl/index.html) (exchange format for cancer registration)
* [CH EMS (R4)](http://fhir.ch/ig/ch-ems/index.html) (emergency medical service protocol) 


## Running validation

The $validate operation checks whether the content of a resource would be acceptable. The validation can be performed generally (FHIR Core specification) or against a defined profile.

* The matchbox (https://test.ahdis.ch/r4) provides a system wide **$validate** operation (with profile as query parameter)
* If desired apply a **profile** to validate against
* Choose your FHIR **resource** (xml/json)
* Get a response on a validation operation ([Operation Outcome](https://www.hl7.org/fhir/operationoutcome.html)) to provide **information about the outcome** (error, warning and information messages)

## POST requests

* Validate a resource with no profile:   
   http://test.ahdis.ch/r4/$validate HTTP/1.1

* Validate a resource with a profile (e.g. CH Core Patient):
   http://test.ahdis.ch/r4/$validate?profile=http://fhir.ch/ig/ch-core/StructureDefinition/ch-core-patient HTTP/1.1


### Resource to validate
The POST request has the content to validate as the resource body.

![POST request]()

### File format (xml/json)
Make sure to set the Content-/Accept-Type to either `application/fhir+json;fhirVersion=4.0` or `application/fhir+json;fhirVersion=4.0` accordingly.

![Headers](https://github.com/ahdis/test.ahdis.ch/blob/master/images/Transformation-Headers.png)

