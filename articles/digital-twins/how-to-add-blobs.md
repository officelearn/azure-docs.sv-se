---
title: Hur du lägger till BLOB-objekt till objekt i Azure Digital Twins | Microsoft Docs
description: Lär dig hur du lägger till BLOB-objekt till objekt i Azure Digital Twins.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 36f4caac38f2f4891af6f61b78b55c7eff15eae4
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54116746"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Lägg till BLOB-objekt till objekt i Azure Digital Twins

BLOB-lagring är Ostrukturerade framställningar av vanliga filtyper, t.ex. bilder och loggar. Blobbar spåra vilken typ av data som de representerar med hjälp av en MIME-typ (till exempel: ”bild/jpeg”) och metadata (namn, beskrivning, typ och så vidare).

Azure Digital Twins stöder bifoga blobbar till enheter, blanksteg och användare. Blobar kan representera en profilbild för en användare, ett foto av enhet, en video, en karta, en inbyggd programvara zip, JSON-data, en logg, osv.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-an-overview"></a>Ladda upp blobar: en översikt

Du kan använda flera delar begäranden för att ladda upp blobar till specifika slutpunkter och deras respektive funktioner.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Blob-metadata

Förutom **Content-Type** och **Content-Disposition**, Azure Digital Twins blob multipart begäranden måste ange rätt JSON-texten. Vilka JSON-texten att skicka beror på vilken typ av HTTP-begäran-åtgärd som utförs.

Det finns fyra huvudsakliga JSON-scheman:

![JSON-scheman][1]

Swagger-dokumentationen beskriver dessa modell-scheman i alla detaljer.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Läs om hur du använder referensdokumentationen genom att läsa [hur du använder Swagger](./how-to-use-swagger.md).

### <a name="examples"></a>Exempel

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

var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

I båda exemplen:

1. Kontrollera att rubrikerna som inkluderar: `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Kontrollera att brödtexten är flera delar:

   - Den första delen innehåller nödvändiga blobmetadata.
   - Den andra delen innehåller textfilen.

1. Kontrollera att filen har angetts som `Content-Type: text/plain`.

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

Lyckade begäranden returnera en **DeviceBlob** JSON-objekt i svaret. **DeviceBlob** objekt överensstämmer med följande JSON-schemat:

| Attribut | Typ | Beskrivning | Exempel |
| --- | --- | --- | --- |
| **DeviceBlobType** | Sträng | En blob-kategori som kan kopplas till en enhet | `Model` och `Specification` |
| **DeviceBlobSubtype** | Sträng | En blob-underkategori som är mer specifik än **DeviceBlobType** | `PhysicalModel`, `LogicalModel`, `KitSpecification`, och `FunctionalSpecification` |

> [!TIP]
> Använd tabellen ovan för att hantera om har returnerade begärandedata.

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

En PATCH-begäran till samma slutpunkt uppdaterar metadata beskrivningar och skapar nya versioner av blobben. HTTP-begäran görs via PATCH-metoden, tillsammans med alla nödvändiga metadata och multipart formulärdata.

Lyckade åtgärder returnerar en **SpaceBlob** objekt som överensstämmer med följande schema. Du kan använda den för att använda data som returneras.

| Attribut | Typ | Beskrivning | Exempel |
| --- | --- | --- | --- |
| **SpaceBlobType** | Sträng | En blob-kategori som kan kopplas till ett blanksteg | `Map` och `Image` |
| **SpaceBlobSubtype** | Sträng | En blob-underkategori som är mer specifik än **SpaceBlobType** | `GenericMap`, `ElectricalMap`, `SatelliteMap`, och `WayfindingMap` |

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

Den returnerade JSON-filen (**UserBlob** objekt) överensstämmer med följande JSON-modeller:

| Attribut | Typ | Beskrivning | Exempel |
| --- | --- | --- | --- |
| **UserBlobType** | Sträng | En blob-kategori som kan kopplas till en användare | `Image` och `Video` |
| **UserBlobSubtype** |  Sträng | En blob-underkategori som är mer specifik än **UserBlobType** | `ProfessionalImage`, `VacationImage`, och `CommercialVideo` |

## <a name="common-errors"></a>Vanliga fel

Ett vanligt fel är att inte inkludera sidhuvud-information:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>Nästa steg

- Mer information om Swagger-referensdokumentation för Azure Digital Twins [Använd Azure Digital Twins Swagger](how-to-use-swagger.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
