# KEX-Antragsdokumente-Upload-API

> ⚠️ You'll find German domain-specific terms in the documentation, for translations and further explanations please refer to our [glossary](https://docs.api.europace.de/common/glossary/)

## General

This API enables the user to upload a document to an application. The service expects a `POST` request with a JSON document as request body.

> ⚠️ This API is continuously developed. Therefore we expect
> all users to align with the "[Tolerant Reader Pattern](https://martinfowler.com/bliki/TolerantReader.html)", which requires clients to be
> tolerant towards compatible API changes when reading and processing the data. This means:
>
> 1. unknown properties must not result in errors
>
> 2. Strings with a restricted set of values (Enums) must support new unknown values
>
> 3. sensible usage of HTTP status codes, even if they are not explicitly documented
>

<!-- https://opensource.zalando.com/restful-api-guidelines/#108 -->

## Uploading documents

The Antragsdokumente-Upload-API for KreditSmart cases can be accessed at the following URL:

```
https://www.europace2.de/kreditsmart/kex/antraege/dokumente
```

The following properties are available for the request body:

 Request parameter            | Description                                                   | Comment                                                        
------------------------------|---------------------------------------------------------------|----------------------------------------------------------------
 antragsnummer                | Identifier of the application on the Europace2 platform       | Mandatory if no **produktanbieterantragsnummer** is submitted. 
 produktanbieterantragsnummer | Identifier of the application to the relevant Produktanbieter | Mandatory if no **antragsnummer** is submitted.                
 kommentar                    | Comment, that can be displayed in the GUI                     | Optional                                                       
 antragsdokument              | The document to be uploaded                                   | Mandatory                                                      
 antragsdokument.fileName     | The document's file name                                      | Mandatory                                                      
 antragsdokument.fileContent  | The document's content as a base64 encoded string             | Mandatory                                                      

The following HTTP headers will be expected:

 Header Parameter | Description                      | Comment                             |
------------------|----------------------------------|-------------------------------------|
 Content-Type     | Content type of the request body | Always has to be `application/json` |

In case of success the API will respond with an HTTP status `201`.

## Authentication

An authentication is required for each request. This API is secured by the OAuth 2.0 client credentials flow using
the [Authorization-API](https://docs.api.europace.de/privatkredit/authentifizierung/). To use these APIs your OAuth2-Client needs the following scopes:

| Scope                         | Label in Partnermanagement            | Description               |
|-------------------------------|---------------------------------------|---------------------------|
| privatkredit:antrag:schreiben | KreditSmart-Anträge anlegen/verändern | Scope for updating a case |

## HTTP Status Errors

| Error Code | Message      | Description                   |
|------------|--------------|-------------------------------|
| 401        | Unauthorized | Authentication failed         |
| 403        | Forbidden    | The API client misses a scope |

## Examples

The examples shown here can be used for testing by `curl` in the following way:

```sh
curl -v -XPOST https://www.europace2.de/kreditsmart/kex/antraege/dokumente \
	-H 'Content-Type: application/json' \
	-H "Authorization: Bearer ${TOKEN}" \
	-d "${REQUEST_BODY}"
```

### Adding a document with Produktanbieterantragsnummer

The document for an application with the Produktanbieterantragsnummer `12919351` can be uploaded with the following request-body:

```json
{
  "produktanbieterantragsnummer": "12919351",
  "antragsdokument": {
    "fileName": "Kreditvertrag.pdf",
    "fileContent": "....."
  }
}
```

### Adding a document with Antragsnummer

Alternatively, the Antragsnummer can be transferred instead of the Produktanbieterantragsnummer:

```json
{
  "antragsnummer": "985132/1/1",
  "antragsdokument": {
    "fileName": "Kreditvertrag.pdf",
    "fileContent": "....."
  }
}
```

### Adding a document with comment

Furthermore, the request can also contain a comment which is displayed to the users of **Kredit**Smart in addition to the actual document:

```json
{
  "produktanbieterantragsnummer": "12919351",
  "kommentar": "Der Kreditvertrag wurde erstellt. Bitte prüfen und unterschreiben.",
  "antragsdokument": {
    "fileName": "Kreditvertrag.pdf",
    "fileContent": "....."
  }
}
```

If the document already exists in the current application by name, comment and document hash, it will not be added again.

## Terms of use

The APIs are made available under the following [Terms of Use](https://docs.api.europace.de/terms/).
