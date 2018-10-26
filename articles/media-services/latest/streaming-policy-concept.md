---
title: Strömning principer i Azure Media Services | Microsoft Docs
description: Den här artikeln innehåller en förklaring av vad Streaming principer är och hur de används av Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: c5f441fef95989e5c82586d96fc6c10e00a9627c
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085503"
---
# <a name="streaming-policies"></a>Principer för direktuppspelning

I Azure Media Services v3 kan Streaming principer du definiera strömningsprotokoll och alternativ för kryptering för din StreamingLocators. Du kan ange namnet på Streaming principen som du skapade, eller så kan du använda en av de fördefinierade principer för direktuppspelning. De fördefinierade Streaming principerna tillgänglig för tillfället är: 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' och ' Predefined_ MultiDrmStreaming'.

> [!IMPORTANT]
> Om du använder en anpassad [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) bör du skapa en begränsad uppsättning av sådana principer för ditt Media Service-konto, och återanvända dem för dina StreamingLocators när samma krypterings- och protokollalternativ krävs. Media Service-kontot har en kvot för antalet StreamingPolicy-poster. Du bör inte skapa en ny StreamingPolicy för varje StreamingLocator.

## <a name="streamingpolicy-definition"></a>StreamingPolicy definition

I följande tabell visar de StreamingPolicy egenskaper och ger definitionerna.

|Namn|Beskrivning|
|---|---|
|id|Fullständigt kvalificerade resurs-ID för resursen.|
|namn|Namnet på resursen.|
|properties.commonEncryptionCbcs|Konfiguration av CommonEncryptionCbcs|
|properties.commonEncryptionCenc|Konfiguration av CommonEncryptionCenc|
|Properties.Created |Skapandetid för direktuppspelning princip|
|properties.defaultContentKeyPolicyName |Standard-ContentKey som används av den aktuella principen för direktuppspelning|
|properties.envelopeEncryption  |Konfiguration av EnvelopeEncryption|
|properties.noEncryption|Konfigurationer av NoEncryption|
|typ|Typ av resursen.|

Läs den fullständiga definitionen [Streaming principer](https://docs.microsoft.com/rest/api/media/streamingpolicies).

## <a name="filtering-ordering-paging"></a>Filtrering, skrivordning, växling

Media Services har stöd för följande OData-frågealternativ för direktuppspelning principer: 

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

### <a name="filteringordering"></a>Filtrering/ordning

I följande tabell visar hur dessa alternativ kan användas på StreamingPolicy egenskaper: 

|Namn|Filter|Beställa|
|---|---|---|
|id|||
|namn|Eq, ne, ge, le, gt, lt|Stigande och fallande|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|Properties.Created |Eq, ne, ge, le, gt, lt|Stigande och fallande|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|typ|||

### <a name="pagination"></a>Sidbrytning

Sidbrytning stöds för var och en av fyra aktiverade sorteringsordningar. För närvarande är sidstorleken 10.

> [!TIP]
> Du bör alltid använda nästa länk för att räkna upp samlingen och inte är beroende av en viss storlek.

Om ett frågesvar innehåller många objekt, tjänsten returnerar en ”\@odata.nextLink” egenskapen för att hämta nästa sida i resultatet. Detta kan användas för att bläddra igenom hela resultatmängden. Du kan inte konfigurera sidstorleken. 

Om StreamingPolicy skapas eller tas bort vid bläddring genom samlingen, syns ändringarna i de returnerade resultaten (om dessa ändringar finns i en del av den samling som inte har hämtats.) 

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

## <a name="next-steps"></a>Nästa steg

[Strömma en fil](stream-files-dotnet-quickstart.md)
