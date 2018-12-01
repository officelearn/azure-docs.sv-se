---
title: Lägg till BLOB-objekt till objekt i Azure Digital Twins | Microsoft Docs
description: Förstå hur du lägger till BLOB-objekt till objekt i Azure Digital Twins
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adgera
ms.openlocfilehash: 8a68ba35ddf7caacbf2339d87c5aeef80f470ba4
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725632"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Lägg till BLOB-objekt till objekt i Azure Digital Twins

BLOB-lagring är Ostrukturerade framställningar av vanliga filtyper, t.ex. bilder och loggar. Blobbar hålla reda på vilken typ av data som de representerar med hjälp av en MIME-typ (till exempel: ”bild/jpeg”) och metadata (namn, beskrivning, typ och så vidare).

Azure Digital Twins stöder bifoga blobbar till enheter, blanksteg och användare. Blobar kan representera en profilbild för en användare, ett foto av enhet, en video, en karta eller en logg.

> [!NOTE]
> Den här artikeln förutsätter:
> * Att din instans är korrekt konfigurerad för att ta emot Management API-begäranden.
> * Att du har korrekt autentiseras med hjälp av en REST-klient för ditt val.

## <a name="uploading-blobs-an-overview"></a>Ladda upp blobar: en översikt

Du kan använda flera delar begäranden för att ladda upp blobar till specifika slutpunkter och deras respektive funktioner.

> [!IMPORTANT]
> Flera delar begäranden kräver tre uppgifter:
> * En **Content-Type** rubrik:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * En **Content-Disposition**: `form-data; name="metadata"`
> * Filinnehållet att ladda upp
>
> Den **Content-Type** och **Content-Disposition** information kan variera beroende på scenario för användning.

Flera delar begäranden som görs till Azure Digital Twins Management API: erna har två delar:

* BLOB-metadata, till exempel en tillhörande MIME-typ, enligt den **Content-Type** och **Content-Disposition** information

* BLOB-innehållet (Ostrukturerade innehållet i filen)  

Ingen av de två delarna som krävs för **KORRIGERA** begäranden. Båda värdena krävs för **POST** eller skapa-åtgärder.

### <a name="blob-metadata"></a>Blob-metadata

Förutom **Content-Type** och **Content-Disposition**, multipart begäranden måste ange rätt JSON-texten. Vilka JSON-texten att skicka beror på vilken typ av HTTP-begäran-åtgärd som utförs.

Det finns fyra huvudsakliga JSON-scheman:

![JSON-scheman][1]

Swagger-dokumentationen beskriver dessa modell-scheman i alla detaljer.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Läs om hur du använder referensdokumentationen genom att läsa [hur du använder Swagger](./how-to-use-swagger.md).

### <a name="examples"></a>Exempel

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Att göra en **POST** begäran som laddar upp en textfil som en blob och kopplar det till ett blanksteg:

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

## <a name="api-endpoints"></a>API-slutpunkter

I följande avsnitt får du via core slutpunkter och deras funktioner.

### <a name="devices"></a>Enheter

Du kan koppla blobar till enheter. Följande bild visar Swagger-referensdokumentation för dina Management-API: er. Den anger alla nödvändiga parametrarna att skicka till dem och enhetsrelaterade API-slutpunkter för blob-användning.

![Enheten blobar][2]

Till exempel om du vill uppdatera eller skapa en blob och bifoga blob till en enhet, göra en **KORRIGERA** begäran om att:

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

Till exempel för att returnera en blob som är kopplat till ett blanksteg, göra en **hämta** begäran om att:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parameter | Ersätt med |
| --- | --- |
| *YOUR_BLOB_ID* | Önskad blob-ID. |

Gör en **KORRIGERA** begäran till samma slutpunkt kan du uppdatera en metadatabeskrivning och skapa en ny version av blobben. HTTP-begäran skickas via den **KORRIGERA** metod, tillsammans med alla nödvändiga metadata och formulärdata i flera delar.

Lyckade åtgärder returnerar en **SpaceBlob** objekt som överensstämmer med följande schema. Du kan använda den för att använda data som returneras.

| Attribut | Typ | Beskrivning | Exempel |
| --- | --- | --- | --- |
| **SpaceBlobType** | Sträng | En blob-kategori som kan kopplas till ett blanksteg | `Map` och `Image` |
| **SpaceBlobSubtype** | Sträng | En blob-underkategori som är mer specifik än **SpaceBlobType** | `GenericMap`, `ElectricalMap`, `SatelliteMap`, och `WayfindingMap` |

### <a name="users"></a>Användare

Du kan koppla blobar till användaren modeller (t.ex, för att associera en profilbild). Följande bild visar relevanta användare API-slutpunkter och eventuella nödvändiga sökvägen-parametrar som `id`:

![Användaren blobar][4]

Till exempel om du vill hämta en blob som är kopplade till en användare, gör en **hämta** begäran med alla nödvändiga formulärdata till:

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

Ett vanligt fel inkluderar inte rätt rubrikinformation:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

## <a name="next-steps"></a>Nästa steg

Mer information om Swagger-referensdokumentation för Azure Digital Twins [Använd Azure Digital Twins Swagger](how-to-use-swagger.md).

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
