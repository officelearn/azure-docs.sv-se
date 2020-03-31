---
title: Filtrering, beställning och personsökning av Medietjänster
titleSuffix: Azure Media Services
description: Lär dig mer om filtrering, beställning och växling av Azure Media Services v3-entiteter.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: bc5c983bc98c3b62df977c6765978cd45cd3c93b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500042"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Filtrering, beställning och personsökning av Medietjänster

I det här avsnittet beskrivs OData-frågealternativen och sidnumreringssupporten som är tillgänglig när du listar Azure Media Services v3-entiteter.

## <a name="considerations"></a>Överväganden

* Egenskaper för entiteter `Datetime` av typen är alltid i UTC-format.
* Tomt utrymme i frågesträngen ska URL-kodas innan du skickar en begäran.

## <a name="comparison-operators"></a>Jämförelseoperatorer

Du kan använda följande operatorer för att jämföra ett fält med ett konstant värde:

Jämställdhetsaktörer:

- `eq`: Testa om ett fält är *lika med* ett konstant värde.
- `ne`: Testa om ett fält inte är *lika med* ett konstant värde.

Range operatörer:

- `gt`: Testa om ett fält är *större än* ett konstant värde.
- `lt`: Testa om ett fält är *mindre än* ett konstant värde.
- `ge`: Testa om ett fält är *större än eller lika med* ett konstant värde.
- `le`: Testa om ett fält är *mindre än eller lika med* ett konstant värde.

## <a name="filter"></a>Filter

Används `$filter` för att ange en OData-filterparameter för att bara hitta de objekt du är intresserad av.

Följande REST-exempel filtrerar värdet på `alternateId` en tillgång:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

Följande C#-exempel filtrerar på tillgångens skapade datum:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Beställ efter

Används `$orderby` för att sortera de returnerade objekten efter den angivna parametern. Ett exempel:  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Om du vill sortera resultaten i stigande eller `asc` `desc` fallande ordning lägger du till antingen eller till fältnamnet, avgränsat med ett blanksteg. Till exempel: `$orderby properties/created desc`.

## <a name="skip-token"></a>Hoppa över token

Om ett frågesvar innehåller många artiklar `$skiptoken` returnerar tjänsten ett (`@odata.nextLink`) värde som du använder för att få nästa resultatsida. Använd den för att bläddra igenom hela resultatuppsättningen.

I Media Services v3 kan du inte konfigurera sidstorleken. Sidstorleken varierar beroende på typ av entitet. Läs de enskilda avsnitten som följer för mer information.

Om entiteter skapas eller tas bort medan du söker igenom samlingen återspeglas ändringarna i de returnerade resultaten (om dessa ändringar finns i den del av samlingen som inte har hämtats).

> [!TIP]
> Använd `nextLink` alltid för att räkna upp samlingen och beror inte på en viss sidstorlek.
>
> Värdet `nextLink` kommer bara att finnas om det finns mer än en sida med entiteter.

Tänk på följande `$skiptoken` exempel på var används. Se till att du *ersätter amstestaccount* med ditt kontonamn och ange *api-versionsvärdet* till den senaste versionen.

Om du begär en lista över tillgångar som denna:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Du får tillbaka ett svar som liknar det här:

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Du skulle sedan begära nästa sida genom att skicka en få begäran om:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

Följande C#-exempel visar hur du räknar upp genom alla streamingpositionerare i kontot.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Använda logiska operatorer för att kombinera frågealternativ

Media Services v3 stöder **ELLER** och **AND** logiska operatörer. 

Följande REST-exempel kontrollerar jobbets tillstånd:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

Du skapar samma fråga i C# så här: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Filtrerings- och beställningsalternativ för entiteter

I följande tabell visas hur du kan använda filtrerings- och beställningsalternativen på olika entiteter:

|Entitetsnamn|Egenskapsnamn|Filter|Beställa|
|---|---|---|---|
|[Tillgångar](https://docs.microsoft.com/rest/api/media/assets/)|namn|`eq`, `gt`, `lt`, `ge`, `le`|`asc` och `desc`|
||egenskaper.alternateId |`eq`||
||egenskaper.assetId |`eq`||
||egenskaper.skapas| `eq`, `gt`, `lt`| `asc` och `desc`|
|[Viktiga principer för innehåll](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|namn|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` och `desc`|
||egenskaper.skapas    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` och `desc`|
||egenskaper.beskrivning    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||properties.lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` och `desc`|
||egenskaper.policyId|`eq`, `ne`||
|[Jobb](https://docs.microsoft.com/rest/api/media/jobs)| namn  | `eq`            | `asc` och `desc`|
||egenskaper.tillstånd        | `eq`, `ne`        |                         |
||egenskaper.skapas      | `gt`, `ge`, `lt`, `le`| `asc` och `desc`|
||properties.lastModified | `gt`, `ge`, `lt`, `le` | `asc` och `desc`| 
|[Streaming locators](https://docs.microsoft.com/rest/api/media/streaminglocators)|namn|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` och `desc`|
||egenskaper.skapas    |`eq`, `ne`, `ge`, `le`,  `gt`, `lt`|`asc` och `desc`|
||egenskaper.endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` och `desc`|
|[Strategier för direktuppspelning](https://docs.microsoft.com/rest/api/media/streamingpolicies)|namn|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` och `desc`|
||egenskaper.skapas    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` och `desc`|
|[Transformering](https://docs.microsoft.com/rest/api/media/transforms)| namn | `eq`            | `asc` och `desc`|
|| egenskaper.skapas      | `gt`, `ge`, `lt`, `le`| `asc` och `desc`|
|| properties.lastModified | `gt`, `ge`, `lt`, `le`| `asc` och `desc`|

## <a name="next-steps"></a>Nästa steg

* [Lista tillgångar](https://docs.microsoft.com/rest/api/media/assets/list)
* [Ange principer för innehållsinnehåll](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [Lista jobb](https://docs.microsoft.com/rest/api/media/jobs/list)
* [Lista principer för direktuppspelning](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [Lista uppspelare](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [Strömma en fil](stream-files-dotnet-quickstart.md)
* [Kvoter och begränsningar](limits-quotas-constraints.md)
