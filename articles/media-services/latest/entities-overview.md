---
title: 'Utveckla med v3 API: er – Azure | Microsoft Docs'
description: 'Den här artikeln beskrivs regler som gäller för entiteter och API: er när du utvecklar med Media Services v3.'
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/30/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6bf0efd6994315d56e7b1b2447ffed9154cf5ee5
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804878"
---
# <a name="developing-with-media-services-v3-apis"></a>Utveckla med Media Services v3-API: er

Den här artikeln beskrivs regler som gäller för entiteter och API: er när du utvecklar med Media Services v3.

## <a name="naming-conventions"></a>Namngivningskonventioner

Azure Media Services v3-resursnamn (till exempel tillgångar, jobb, transformeringar) är föremål Azure Resource Manager-namnbegränsningar. I enlighet med Azure Resource Manager är resursnamnen alltid unika. Därför kan du använda alla strängar som unika identifierare (till exempel GUID) för ditt resursnamn. 

Media Services resursnamn får inte innehålla: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', apostrof eller några andra kontrolltecken. Alla andra tecken tillåts. Maxlängden för ett resursnamn är 260 tecken. 

Mer information om namngivning av Azure Resource Manager finns i: [Namngivningskrav](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) och [Namngivningskonventioner](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles"></a>Designprinciper för v3 API

En av de viktigaste designprinciperna för v3 API är att göra API:et säkrare. v3 API:er returnerar inte hemlighet eller autentiseringsuppgifter för en åtgärd av typen **hämta** eller **lista**. Nycklarna är alltid null, tomma eller oberoende av svaret. Du måste anropa en separat åtgärdsmetod för att få hemlighet och autentiseringsuppgifter. Med separata åtgärder kan du ställa in olika RBAC-säkerhetsbehörigheter om vissa API:er hämtar/visar hemligheter medan andra API:er inte gör det. Information om hur du hanterar åtkomst med RBAC finns i dokumentationen om att [hantera åtkomst med RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Exempel på detta kan vara:

* Returnerar inte ContentKey värden i Get för StreamingLocator.
* Returnerar inte nycklar för begränsning av Get för ContentKeyPolicy.
* inte returnerar frågan sträng en del av URL-Adressen (för att ta bort signaturen) till URL: er för jobbets HTTP-indata.

Se exemplet [Hämta princip för innehållsnyckel – .NET](get-content-key-policy-dotnet-howto.md).

## <a name="long-running-operations"></a>Långvariga åtgärder

Åtgärderna som markerats med `x-ms-long-running-operation` i Azure Media Services [swagger filer](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) är långa åtgärder. 

Mer information om hur du spårar asynkrona åtgärder i Azure finns [asynkrona åtgärder](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)

Media Services har följande långvariga åtgärder:

* Skapa LiveEvent
* Uppdatera LiveEvent
* Delete LiveEvent
* Starta LiveEvent
* Stoppa LiveEvent
* Återställ LiveEvent
* Skapa LiveOutput
* Delete LiveOutput
* Skapa StreamingEndpoint
* Uppdatera StreamingEndpoint
* Ta bort StreamingEndpoint
* Starta StreamingEndpoint
* Stoppa StreamingEndpoint
* Skala StreamingEndpoint


## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrering, skrivordning, växling av Media Services-entiteter

Media Services har stöd för följande OData-frågealternativ för Media Services v3 entiteter: 

* $filter 
* $orderby 
* $top 
* $skiptoken 

Operatorn beskrivning:

* EQ = lika med
* Ne = inte lika med
* Ge = större än eller lika med
* Le = mindre än eller lika med
* Gt = större än
* Lt = mindre än

Egenskaper för entiteter som är av typen Datetime är alltid i UTC-format.

### <a name="page-results"></a>Sidan resultat

Om ett frågesvar innehåller många objekt, tjänsten returnerar en ”\@odata.nextLink” egenskapen för att hämta nästa sida i resultatet. Detta kan användas för att bläddra igenom hela resultatmängden. Du kan inte konfigurera sidstorleken. Sidstorleken varierar beroende på typen av enhet, Läs enskilda avsnitten som följer mer information.

Om entiteter är skapas eller tas bort vid bläddring genom samlingen, visas ändringarna i de returnerade resultaten (om dessa ändringar finns i en del av den samling som inte har hämtats). 

> [!TIP]
> Du bör alltid använda nästa länk för att räkna upp samlingen och inte är beroende av en viss storlek.

### <a name="assets"></a>Tillgångar

#### <a name="filteringordering"></a>Filtrering/ordning

I följande tabell visas hur filtrering och sortering alternativ kan användas på den [tillgången](https://docs.microsoft.com/rest/api/media/assets) egenskaper: 

|Namn|Filter|Beställa|
|---|---|---|
|id|||
|namn|eq, gt, lt| Stigande och fallande|
|properties.alternateId |eq||
|properties.assetId |eq||
|Properties.container |||
|Properties.Created| eq, gt, lt| Stigande och fallande|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|typ|||

I följande C#-exempel filtrerar på Skapad datum:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

#### <a name="pagination"></a>Sidbrytning 

Sidbrytning stöds för var och en av fyra aktiverade sorteringsordningar. Sidstorleken är för närvarande 1 000.

##### <a name="c-example"></a>C#-exempel

I följande C#-exempel visar hur du räknar upp via alla tillgångar i kontot.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

##### <a name="rest-example"></a>REST-exempel

Överväg följande exempel visar där $skiptoken används. Kontrollera att du ersätter *amstestaccount* med ditt kontonamn och ange den *api-versionen* värde till den senaste versionen.

Om du begär en lista över tillgångar så här:

```
GET  https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Du skulle få tillbaka ett svar ungefär så här:

```
HTTP/1.1 200 OK
 
{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-5a4f-470a-9d81-6037d7c23eff","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-98d07299-5a4f-470a-9d81-6037d7c23eff","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-912e-447b-a1ed-0f723913b20d","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-fd05a503-912e-447b-a1ed-0f723913b20d","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Du kan sedan begära nästa sida genom att skicka en get-begäran:

```
https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

Fler REST-exempel finns i [tillgångar - listan](https://docs.microsoft.com/rest/api/media/assets/list)

### <a name="content-key-policies"></a>Viktiga innehållsprinciper

#### <a name="filteringordering"></a>Filtrering/ordning

Följande tabell visar hur dessa alternativ kan användas på den [Innehållsprinciper nyckel](https://docs.microsoft.com/rest/api/media/contentkeypolicies) egenskaper: 

|Namn|Filter|Beställa|
|---|---|---|
|id|||
|namn|eq, ne, ge, le, gt, lt|Stigande och fallande|
|Properties.Created |eq, ne, ge, le,  gt, lt|Stigande och fallande|
|properties.description |eq, ne, ge, le, gt, lt||
|properties.lastModified|eq, ne, ge, le, gt, lt|Stigande och fallande|
|Properties.Options |||
|properties.policyId|eq, ne||
|typ|||

#### <a name="pagination"></a>Sidbrytning

Sidbrytning stöds för var och en av fyra aktiverade sorteringsordningar. För närvarande är sidstorleken 10.

Följande C# exempel visas hur du räknar upp igenom alla **nyckel Innehållsprinciper** i kontot.

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

REST-exempel finns i [nyckel Innehållsprinciper - lista](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)

### <a name="jobs"></a>Jobb

#### <a name="filteringordering"></a>Filtrering/ordning

Följande tabell visar hur dessa alternativ kan användas på den [jobb](https://docs.microsoft.com/rest/api/media/jobs) egenskaper: 

| Namn    | Filter                        | Beställa |
|---------|-------------------------------|-------|
| namn                    | eq            | Stigande och fallande|
| properties.state        | eq, ne        |                         |
| Properties.Created      | gt, ge, lt, le| Stigande och fallande|
| properties.lastModified | gt, ge, lt, le | Stigande och fallande| 


#### <a name="pagination"></a>Sidbrytning

Jobb sidbrytning stöds i Media Services v3.

Följande C# exempel visas hur du räknar upp via jobb i kontot.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

REST-exempel finns i [jobb – lista](https://docs.microsoft.com/rest/api/media/jobs/list)

### <a name="streaming-locators"></a>Positionerare för direktuppspelning

#### <a name="filteringordering"></a>Filtrering/ordning

I följande tabell visar hur dessa alternativ kan användas på StreamingLocator egenskaper: 

|Namn|Filter|Beställa|
|---|---|---|
|id |||
|namn|eq, ne, ge, le, gt, lt|Stigande och fallande|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|Properties.Created |eq, ne, ge, le,  gt, lt|Stigande och fallande|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |eq, ne, ge, le, gt, lt|Stigande och fallande|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|typ   |||

#### <a name="pagination"></a>Sidbrytning

Sidbrytning stöds för var och en av fyra aktiverade sorteringsordningar. För närvarande är sidstorleken 10.

I följande C#-exempel visar hur du räknar upp via alla StreamingLocators i kontot.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

REST-exempel finns i [Streaming positionerare - lista](https://docs.microsoft.com/rest/api/media/streaminglocators/list)

### <a name="streaming-policies"></a>Principer för direktuppspelning

#### <a name="filteringordering"></a>Filtrering/ordning

I följande tabell visar hur dessa alternativ kan användas på StreamingPolicy egenskaper: 

|Namn|Filter|Beställa|
|---|---|---|
|id|||
|namn|eq, ne, ge, le, gt, lt|Stigande och fallande|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|Properties.Created |eq, ne, ge, le,  gt, lt|Stigande och fallande|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|typ|||

#### <a name="pagination"></a>Sidbrytning

Sidbrytning stöds för var och en av fyra aktiverade sorteringsordningar. För närvarande är sidstorleken 10.

I följande C#-exempel visar hur du räknar upp via alla StreamingPolicies i kontot.

```csharp
var firstPage = await MediaServicesArmClient.StreamingPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

REST-exempel finns i [Streaming principer - lista](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)


### <a name="transform"></a>Transformering

#### <a name="filteringordering"></a>Filtrering/ordning

Följande tabell visar hur dessa alternativ kan användas på den [omvandlar](https://docs.microsoft.com/rest/api/media/transforms) egenskaper: 

| Namn    | Filter                        | Beställa |
|---------|-------------------------------|-------|
| namn                    | eq            | Stigande och fallande|
| Properties.Created      | gt, ge, lt, le| Stigande och fallande|
| properties.lastModified | gt, ge, lt, le | Stigande och fallande|

## <a name="next-steps"></a>Nästa steg

[Strömma en fil](stream-files-dotnet-quickstart.md)
