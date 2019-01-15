---
title: Hur du lägger till BLOB-objekt till objekt i Azure Digital Twins | Microsoft Docs
description: Lär dig hur du lägger till BLOB-objekt till objekt i Azure Digital Twins.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: ffd7d71c33b569b396b9f8babf8105968ee525b9
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263075"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Lägg till BLOB-objekt till objekt i Azure Digital Twins

BLOB-lagring är Ostrukturerade framställningar av vanliga filtyper, t.ex. bilder och loggar. Blobbar spåra vilken typ av data som de representerar med hjälp av en MIME-typ (till exempel: ”bild/jpeg”) och metadata (namn, beskrivning, typ och så vidare).

Azure Digital Twins stöder bifoga blobbar till enheter, blanksteg och användare. Blobar kan representera en profilbild för en användare, ett foto av enhet, en video, en karta, en inbyggd programvara zip, JSON-data, en logg, osv.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Ladda upp blobar översikt

Du kan använda flera delar begäranden för att ladda upp blobar till specifika slutpunkter och deras respektive funktioner.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Blob-metadata

Förutom **Content-Type** och **Content-Disposition**, Azure Digital Twins blob multipart begäranden måste ange rätt JSON-texten. Vilka JSON-texten att skicka beror på vilken typ av HTTP-begäran-åtgärd som utförs.

Det finns fyra huvudsakliga JSON-scheman:

![JSON-scheman][1]

JSON-blob-metadata överensstämmer med följande modell:

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
| **parentId** | Sträng | Den överordnade entiteten associera bloben med (blanksteg, enheter eller användare) |
| **Namn** |Sträng | Ett mänskliga eget namn för blobben |
| **typ** | Sträng | Vilken typ av blob - kan inte använda *typ* och *typeId*  |
| **typeId** | Integer | Blob typ-ID - kan inte använda *typ* och *typeId* |
| **Undertyp** | Sträng | Blob-undertyp - kan inte använda *undertyp* och *subtypeId* |
| **subtypeId** | Integer | Undertyp-ID: T för blob - kan inte använda *undertyp* och *subtypeId* |
| **Beskrivning** | Sträng | Anpassade beskrivning av blob |
| **delning** | Sträng | Om blobben som kan delas - enum [`None`, `Tree`, `Global`] |

BLOB-metadata alltid anges som det första segmentet med **Content-Type** `application/json` eller som en `.json` fil. Fildata har angetts i det andra segmentet och kan vara vilken MIME-typ som stöds.

Swagger-dokumentationen beskriver dessa modell-scheman i alla detaljer.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Läs om hur du använder referensdokumentationen genom att läsa [hur du använder Swagger](./how-to-use-swagger.md).

<div id="blobModel"></div>

### <a name="blobs-response-data"></a>Svarsdata för blobbar

Individuellt returnerade blobar överensstämmer med följande JSON-schemat:

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
| **ID** | Sträng | Den unika identifieraren för bloben |
| **Namn** |Sträng | Ett mänskliga eget namn för blobben |
| **parentId** | Sträng | Den överordnade entiteten associera bloben med (blanksteg, enheter eller användare) |
| **typ** | Sträng | Vilken typ av blob - kan inte använda *typ* och *typeId*  |
| **typeId** | Integer | Blob typ-ID - kan inte använda *typ* och *typeId* |
| **Undertyp** | Sträng | Blob-undertyp - kan inte använda *undertyp* och *subtypeId* |
| **subtypeId** | Integer | Undertyp-ID: T för blob - kan inte använda *undertyp* och *subtypeId* |
| **delning** | Sträng | Om blobben som kan delas - enum [`None`, `Tree`, `Global`] |
| **Beskrivning** | Sträng | Anpassade beskrivning av blob |
| **contentInfos** | Matris | Anger Ostrukturerade metadatainformation inklusive version |
| **FullName** | Sträng | Det fullständiga namnet på bloben |
| **spacePaths** | Sträng | Sökvägen utrymme |

BLOB-metadata alltid anges som det första segmentet med **Content-Type** `application/json` eller som en `.json` fil. Fildata har angetts i det andra segmentet och kan vara vilken MIME-typ som stöds.

### <a name="blob-multipart-request-examples"></a>Exempel för BLOB-multipart begäran

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

För att ladda upp en textfil som en blob och associera det med ett blanksteg, gör du en autentiserad HTTP POST-begäran till:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

Med följande text:

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
| USER_DEFINED_BOUNDARY | Ett namn i flera delar innehåll gräns |

Följande kod är en .NET-implementering av samma blob överföringen, med hjälp av klassen [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

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

Till sist [cURL](https://curl.haxx.se/) användare kan göra anrop för multipart formulär på samma sätt:

![Enheten blobar][5]

```bash
curl
 -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs"
 -H "Authorization: Bearer YOUR_TOKEN"
 -H "Accept: application/json"
 -H "Content-Type: multipart/form-data"
 -F "meta={\"ParentId\": \"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\": \"A well chosen description\", \"Sharing\": \"None\"};type=application/json"
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Värde | Ersätt med |
| --- | --- |
| YOUR_TOKEN | Din giltig OAuth 2.0-token |
| YOUR_SPACE_ID | ID för utrymmet som ska associera blobben med |
| PATH_TO_FILE | Sökvägen till textfilen |

En lyckad POST returnerar ID för den nya bloben (tidigare markerat i rött).

## <a name="api-endpoints"></a>API-slutpunkter

I följande avsnitt beskrivs core blob-relaterade API-slutpunkter och deras funktioner.

### <a name="devices"></a>Enheter

Du kan koppla blobar till enheter. Följande bild visar Swagger-referensdokumentation för dina Management-API: er. Den anger alla nödvändiga parametrarna att skicka till dem och enhetsrelaterade API-slutpunkter för blob-användning.

![Enheten blobar][2]

Till exempel om du vill uppdatera eller skapa en blob och bifoga blob till en enhet, gör du en autentiserad HTTP PATCH-begäran till:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_BLOB_ID* | Önskad blob-ID. |

Lyckade begäranden som returnerar ett JSON-objekt som [ovan](#blobModel).

### <a name="spaces"></a>Blanksteg

Du kan även bifoga blobbar i blanksteg. Följande bild visar en lista över alla utrymme API-slutpunkter ansvarar för att hantera blobar. Den visar också eventuella sökvägsparametrar att skicka till dessa slutpunkter.

![Utrymme blobar][3]

Till exempel för att returnera en blob som är kopplat till ett utrymme, gör du en autentiserad HTTP GET-begäran till:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_BLOB_ID* | Önskad blob-ID. |

Lyckade begäranden som returnerar ett JSON-objekt som [ovan](#blobModel).

En PATCH-begäran till samma slutpunkt uppdaterar metadata beskrivningar och skapar versioner av blobben. HTTP-begäran görs via PATCH-metoden, tillsammans med alla nödvändiga metadata och multipart formulärdata.

### <a name="users"></a>Användare

Du kan koppla blobar till användaren modeller (t.ex, för att associera en profilbild). Följande bild visar relevanta användare API-slutpunkter och eventuella nödvändiga sökvägen-parametrar som `id`:

![Användaren blobar][4]

Till exempel om du vill hämta en blob som är kopplade till en användare, gör du en autentiserad HTTP GET-begäran med alla nödvändiga formulärdata till:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_BLOB_ID* | Önskad blob-ID. |

Lyckade begäranden som returnerar ett JSON-objekt som [ovan](#blobModel).

## <a name="common-errors"></a>Vanliga fel

Ett vanligt fel omfattar inte med rätt huvudinformation:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

Lös felet genom att kontrollera att övergripande begäran har en lämplig **Content-Type** rubrik:

* `multipart/mixed`
* `multipart/form-data`

Kontrollera också att varje multipart segment har en motsvarande **Content-Type** efter behov.

## <a name="next-steps"></a>Nästa steg

- Mer information om Swagger-referensdokumentation för Azure Digital Twins [Använd Azure Digital Twins Swagger](how-to-use-swagger.md).

- Om du vill ladda upp blobar via Postman, läsa [så här konfigurerar du Postman](./how-to-configure-postman.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
[5]: media/how-to-add-blobs/curl.PNG