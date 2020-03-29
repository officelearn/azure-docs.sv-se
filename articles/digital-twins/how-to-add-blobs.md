---
title: Så här lägger du till blobbar i objekt – Azure Digital Twins | Microsoft-dokument
description: Lär dig hur du lägger till blobbar till användare, enheter och utrymmen i Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: c85db05e6feeea43023c2391998f837348caed4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75929643"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Lägga till blobbar i objekt i Azure Digital Twins

Blobbar är ostrukturerade representationer av vanliga filtyper, som bilder och loggar. Blobbar spårar vilken typ av data de representerar med hjälp av en MIME-typ (till exempel: "image/jpeg") och metadata (namn, beskrivning, typ och så vidare).

Azure Digital Twins stöder att bifoga blobbar till enheter, utrymmen och användare. Blobbar kan representera en profilbild för en användare, ett enhetsfoto, en video, en karta, en zip-zip, JSON-data, en logg osv.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Ladda upp blobbar översikt

Du kan använda flerdelade begäranden för att ladda upp blobbar till specifika slutpunkter och deras respektive funktioner.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Blob-metadata

Förutom **Content-Type** och Content-Disposition måste Azure Digital Twins blob-flerdelsbegäranden ange rätt **JSON-brödtext.** Vilket JSON-brödtext som ska skickas beror på vilken typ av HTTP-begäran som utförs.

De fyra viktigaste JSON-schemana är:

[![JSON-scheman](media/how-to-add-blobs/blob-models-swagger-img.png)](media/how-to-add-blobs/blob-models-swagger-img.png#lightbox)

JSON blob metadata överensstämmer med följande modell:

```JSON
{
    "parentId": "00000000-0000-0000-0000-000000000000",
    "name": "My First Blob",
    "type": "Map",
    "subtype": "GenericMap",
    "description": "A well chosen description",
    "sharing": "None"
  }
```

| Attribut | Typ | Beskrivning |
| --- | --- | --- |
| **parentId** | String | Den överordnade entiteten som ska associeras med blobben med (mellanslag, enheter eller användare) |
| **Namn** |String | Ett människovänligt namn för blobben |
| **Typ** | String | Typen av blob - kan inte använda *typ* och *typeId*  |
| **typeId (typeId)** | Integer | Blob-typ-ID - kan inte använda *typ* och *typeId* |
| **Subtyp** | String | Blob-undertypen - kan inte använda *undertyp* och *subtypeId* |
| **subtypeId** | Integer | Undertyp-ID för blobben - kan inte använda *undertyp* och *subtypeId* |
| **Beskrivning** | String | Anpassad beskrivning av blobben |
| **Dela** | String | Om blobben kan delas -`None` `Tree`uppräkning [ , `Global`] |

Blob-metadata anges alltid som det första segmentet `.json` med **Content-Type** `application/json` eller som en fil. Fildata levereras i det andra segmentet och kan vara av alla MIME-typer som stöds.

Swagger-dokumentationen beskriver dessa modellscheman i detalj.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Lär dig mer om hur du använder referensdokumentationen genom att läsa [Så här använder du Swagger](./how-to-use-swagger.md).

### <a name="blobs-response-data"></a>Blobbar svarsdata

Individuellt returnerade blobbar överensstämmer med följande JSON-schema:

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "parentId": "00000000-0000-0000-0000-000000000000",
  "type": "string",
  "subtype": "string",
  "typeId": 0,
  "subtypeId": 0,
  "sharing": "None",
  "description": "string",
  "contentInfos": [
    {
      "type": "string",
      "sizeBytes": 0,
      "mD5": "string",
      "version": "string",
      "lastModifiedUtc": "2019-01-12T00:58:08.689Z",
      "metadata": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    }
  ],
  "fullName": "string",
  "spacePaths": [
    "string"
  ]
}
```

| Attribut | Typ | Beskrivning |
| --- | --- | --- |
| **Id** | String | Den unika identifieraren för blobben |
| **Namn** |String | Ett människovänligt namn för blobben |
| **parentId** | String | Den överordnade entiteten som ska associeras med blobben med (mellanslag, enheter eller användare) |
| **Typ** | String | Typen av blob - kan inte använda *typ* och *typeId*  |
| **typeId (typeId)** | Integer | Blob-typ-ID - kan inte använda *typ* och *typeId* |
| **Subtyp** | String | Blob-undertypen - kan inte använda *undertyp* och *subtypeId* |
| **subtypeId** | Integer | Undertyp-ID för blobben - kan inte använda *undertyp* och *subtypeId* |
| **Dela** | String | Om blobben kan delas -`None` `Tree`uppräkning [ , `Global`] |
| **Beskrivning** | String | Anpassad beskrivning av blobben |
| **innehållInfos** | Matris | Anger ostrukturerad metadatainformation inklusive version |
| **Fullname** | String | Det fullständiga namnet på blobben |
| **spacePaths (uttr)** | String | Utrymmesbanan |

Blob-metadata anges alltid som det första segmentet `.json` med **Content-Type** `application/json` eller som en fil. Fildata levereras i det andra segmentet och kan vara av alla MIME-typer som stöds.

### <a name="blob-multipart-request-examples"></a>Exempel på flerad-begäranden i Blob

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Om du vill ladda upp en textfil som en blob och associera den med ett blanksteg gör du en autentiserat HTTP POST-begäran till:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

Med följande kropp:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| Värde | Ersätt med |
| --- | --- |
| USER_DEFINED_BOUNDARY | Ett gränsnamn för flera delar |

Följande kod är en .NET-implementering av samma blob-uppladdning med klassen [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

//MY_BLOB.txt is the String representation of your text file
var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

Slutligen kan [cURL-användare](https://curl.haxx.se/) göra förfrågningar i flera delar på samma sätt:

```bash
curl -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs" \
 -H "Authorization: Bearer YOUR_TOKEN" \
 -H "Accept: application/json" \
 -H "Content-Type: multipart/form-data" \
 -F "meta={\"ParentId\":\"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob\",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\":\"A well chosen description\",\"Sharing\":\"None\"};type=application/json" \
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Värde | Ersätt med |
| --- | --- |
| YOUR_TOKEN | Din giltiga OAuth 2.0-token |
| YOUR_SPACE_ID | ID:et för det utrymme som ska associeras med blobben |
| PATH_TO_FILE | Sökvägen till textfilen |

[![cURL exempel](media/how-to-add-blobs/http-blob-post-through-curl-img.png)](media/how-to-add-blobs/http-blob-post-through-curl-img.png#lightbox)

Ett lyckat POST returnerar ID för den nya bloben.

## <a name="api-endpoints"></a>API-slutpunkter

I följande avsnitt beskrivs de grundläggande blob-relaterade API-slutpunkterna och deras funktioner.

### <a name="devices"></a>Enheter

Du kan koppla blobbar till enheter. Följande bild visar Swagger-referensdokumentationen för dina hanterings-API:er. Den anger enhetsrelaterade API-slutpunkter för blob-förbrukning och eventuella nödvändiga sökvägsparametrar för att gå vidare till dem.

[![Enhetsblobar](media/how-to-add-blobs/blobs-device-api-swagger-img.png)](media/how-to-add-blobs/blobs-device-api-swagger-img.png#lightbox)

Om du till exempel vill uppdatera eller skapa en blob och bifoga blobben till en enhet gör du en autentiserat HTTP PATCH-begäran till:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_BLOB_ID* | Önskat blob-ID |

Lyckade begäranden returnerar ett JSON-objekt [enligt beskrivningen tidigare](#blobs-response-data).

### <a name="spaces"></a>Utrymmen

Du kan också koppla blobbar till blanksteg. Följande bild visar alla utrymme API-slutpunkter som ansvarar för hantering av blobbar. Den visar också alla sökvägsparametrar för att gå vidare till dessa slutpunkter.

[![Utrymme blobbar](media/how-to-add-blobs/blobs-space-api-swagger-img.png)](media/how-to-add-blobs/blobs-space-api-swagger-img.png#lightbox)

Om du till exempel vill returnera en blob som är kopplad till ett blanksteg gör du en autentiserat HTTP GET-begäran till:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_BLOB_ID* | Önskat blob-ID |

Lyckade begäranden returnerar ett JSON-objekt [enligt beskrivningen tidigare](#blobs-response-data).

En PATCH-begäran till samma slutpunkt uppdaterar metadatabeskrivningar och skapar versioner av blobben. HTTP-begäran görs via PATCH-metoden, tillsammans med alla nödvändiga metadata- och flerdelsformulärdata.

### <a name="users"></a>Användare

Du kan koppla blobbar till användarmodeller (till exempel för att associera en profilbild). Följande bild visar relevanta användar-API-slutpunkter och `id`alla nödvändiga sökvägsparametrar, till exempel:

[![Användarblobar](media/how-to-add-blobs/blobs-users-api-swagger-img.png)](media/how-to-add-blobs/blobs-users-api-swagger-img.png#lightbox)

Om du till exempel vill hämta en blob som är kopplad till en användare gör du en autentiserat HTTP GET-begäran med nödvändiga formulärdata för att:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_BLOB_ID* | Önskat blob-ID |

Lyckade begäranden returnerar ett JSON-objekt [enligt beskrivningen tidigare](#blobs-response-data).

## <a name="common-errors"></a>Vanliga fel

* Ett vanligt fel innebär att du inte anger rätt huvudinformation:

  ```JSON
  {
      "error": {
          "code": "400.600.000.000",
          "message": "Invalid media type in first section."
      }
  }
  ```

  Lös det här felet genom att kontrollera att den övergripande begäran har ett lämpligt sidhuvud **av innehållstyp:**

     * `multipart/mixed`
     * `multipart/form-data`

  Kontrollera också att varje *segment med flera delar* har en lämplig motsvarande **innehållstyp**.

* Ett andra vanligt fel uppstår när flera blobbar tilldelas samma resurs i diagrammet [spatial intelligens:](concepts-objectmodel-spatialgraph.md)

  ```JSON
  {
      "error": {
          "code": "400.600.000.000",
          "message": "SpaceBlobMetadata already exists."
      }
  }
  ```

  > [!NOTE]
  > **Meddelandeattributet** varierar beroende på resursen. 

  Endast en blob (av varje slag) kan kopplas till varje resurs i det rumsliga diagrammet. 

  LÃ¶s problemet genom att uppdatera den befintliga bloben med hjälp av lämplig API HTTP PATCH-åtgärd. Om du gör det ersätts de befintliga blob-data med önskade data.

## <a name="next-steps"></a>Nästa steg

- Mer information om Swagger-referensdokumentation för Azure Digital Twins finns i [Använd Azure Digital Twins Swagger](how-to-use-swagger.md).

- Om du vill ladda upp blobbar via Postman läser du [Så här konfigurerar du Postman](./how-to-configure-postman.md).