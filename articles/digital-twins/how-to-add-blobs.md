---
title: Hur du lägger till BLOB-objekt till objekt i Azure Digital Twins | Microsoft Docs
description: Förstå hur du lägger till BLOB-objekt till objekt i Azure Digital Twins
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adgera
ms.openlocfilehash: 0929a4a63eee35d21723c980887d6b4217898682
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688165"
---
# <a name="how-to-add-blobs-to-objects-in-azure-digital-twins"></a>Hur du lägger till BLOB-objekt till objekt i Azure Digital Twins

BLOB-lagring är Ostrukturerade framställningar av vanliga filtyper (till exempel bilder och loggar). Blobbar hålla reda på vilken typ av data som de representerar med hjälp av en MIME-typ (till exempel: ”bild/jpeg”) och metadata (namn, beskrivning, typ, osv.).

Azure Digital Twins stöder tillkoppling av BLOB-objekt till enheter, blanksteg och användare. Blobar kan representera en profilbild för en användare, ett foto av enhet, en video, en karta eller en logg.

> [!NOTE]
> Den här artikeln förutsätter:
> * Att din instans är korrekt konfigurerad för att ta emot Management API-begäranden.
> * Att har korrekt autentiseras med en REST-klient för ditt val.

## <a name="uploading-blobs-an-overview"></a>Ladda upp blobar: en översikt

Flera delar begäranden används för att ladda upp Blobar till specifika slutpunkter och deras respektive funktioner.

> [!IMPORTANT]
> Flera delar begäranden kräver tre viktiga uppgifter. För Azure Digital Twins:
> * En **Content-Type** rubrik:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
> * En **Content-Disposition**: `form-data; name="metadata"`.
> * Filens innehåll du överför.
>
> Den exakta **Content-Type** och **Content-Disposition** kan variera beroende på scenario för användning.

Varje multipart begäran är uppdelad i flera segment. Flera delar begäranden som görs till Azure Digital Twins Management API: erna delas upp i **två** (**2**) sådana delar:

1. Den första delen är obligatoriskt och innehåller Blob-metadata, till exempel en tillhörande MIME-typ per den **Content-Type** och **Content-Disposition** ovan.

1. Den andra delen innehåller det faktiska Blob-innehållet (Ostrukturerade innehållet i filen).  

Ingen av de två delarna som krävs för **KORRIGERA** begäranden. Båda värdena krävs för **POST** eller skapa-åtgärder.

### <a name="blob-metadata"></a>Blob-metadata

Förutom **Content-Type** och **Content-Disposition**, multipart begäranden måste även ange rätt JSON-texten. Vilka JSON-texten att skicka beror på vilken typ av HTTP-begäran-åtgärd som utförs.

De fyra huvudsakliga JSON-scheman används är:

![Utrymme blobar][1]

Dessa scheman för modellen beskrivs i alla detaljer i den angivna Swagger-dokumentationen.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Läs om hur du använder den angivna referensdokumentationen genom att läsa [hur du använder Swagger](./how-to-use-swagger.md).

### <a name="examples"></a>Exempel

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Att göra en POST-begäran som laddar upp en textfil som en Blob och kopplar det till ett blanksteg:

```plaintext
POST YOUR_MANAGEMENT_API_URL/spaces/blobs HTTP/1.1
Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"

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

| Parametervärde | Ersätt med |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | Ett namn i flera delar innehåll gräns |

En .NET-implementering av samma Blob upload finns nedan med hjälp av klassen [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metaData in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

## <a name="api-endpoints"></a>API-slutpunkter

Nedan ges en genomgång av grundläggande slutpunkter och deras specifika funktioner.

### <a name="devices"></a>Enheter

Blobar kan kopplas till enheter. Bilden nedan (som illustrerar Swagger-referensdokumentation för dina Management-API: er) anger enhetsrelaterade API-slutpunkter för Blob-förbrukning och eventuella nödvändiga sökvägen-parametrar för att skicka till dem:

![Enheten blobar][2]

Om du vill uppdatera eller skapa en Blob och bifoga Blob till en enhet, till exempel skickas en PATCH-begäran till:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_BLOB_ID* | Den önskade Blob-ID. |

Lyckade begäranden returnerar ett DeviceBlob JSON-objekt i svaret. DeviceBlobs överensstämmer med följande JSON-schemat:

| Attribut | Typ | Beskrivning | Exempel |
| --- | --- | --- | --- |
| **DeviceBlobType** | Sträng | En Blob-kategori som kan kopplas till en enhet | `Model` och `Specification` |
| **DeviceBlobSubtype*** | Sträng | En Blob-underkategori som är större än DeviceBlobType | `PhysicalModel`, `LogicalModel`, `KitSpecification`, och `FunctionalSpecification` |

> [!TIP]
> Använd tabellen ovan för att hantera om har returnerade begärandedata.

### <a name="spaces"></a>Blanksteg

Blobbar kan också kopplas till blanksteg. Bilden nedan visar en lista över alla utrymme API-slutpunkter som ansvarar för att hantera Blobar tillsammans med eventuella sökvägsparametrar att skicka till dem:

![Utrymme blobar][3]

Till exempel gör en GET-begäran för att returnera en Blob som är kopplat till ett blanksteg:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_BLOB_ID* | Den önskade Blob-ID. |

Gör PATCH-begäran till samma slutpunkt kan du uppdatera en metadatabeskrivning och skapa en ny version av blobben. HTTP-begäran görs med PATCH-metoden tillsammans med alla nödvändiga metadata och multipart formulärdata.

Lyckade åtgärder returnerar en SpaceBlob som överensstämmer med följande schema och kan användas för att använda data som returneras:

| Attribut | Typ | Beskrivning | Exempel |
| --- | --- | --- | --- |
| **SpaceBlobType** | Sträng | En Blob-kategori som kan kopplas till ett blanksteg | `Map` och `Image` |
| **SpaceBlobSubtype** | Sträng | En Blob-underkategori som är större än SpaceBlobType | `GenericMap`, `ElectricalMap`, `SatelliteMap`, och `WayfindingMap` |

### <a name="users"></a>Användare

Blobar kan kopplas till användaren modeller (d.v.s. associera en profilbild). Bilden nedan visar relevanta användare API-slutpunkter och alla nödvändiga parametrarna som en `id`:

![Användaren blobar][4]

Till exempel om du vill hämta en Blob som är kopplade till en användare, gör en GET-begäran med alla nödvändiga formulärdata till:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_BLOB_ID* | Den önskade Blob-ID. |

Returnerade JSON-filen (UserBlobs) omfattas av följande JSON-modeller:

| Attribut | Typ | Beskrivning | Exempel |
| --- | --- | --- | --- |
| **UserBlobType** | Sträng | En Blob-kategori som kan kopplas till en användare | `Image` och `Video` |
| **UserBlobSubtype** |  Sträng | En Blob-underkategori som är större än UserBlobType | `ProfessionalImage`, `VacationImage`, och `CommercialVideo` |

## <a name="common-errors"></a>Vanliga fel

Inkluderar inte rätt rubrikinformation:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>Nästa steg

Mer information om angivna referensdokumentation för Azure Digital Twins Swagger [hur du använder digitala Twins Swagger](how-to-use-swagger.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
