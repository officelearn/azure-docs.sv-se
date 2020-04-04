---
title: Kvoter och begränsningar i Azure Media Services
description: I det här avsnittet beskrivs kvoter och begränsningar i Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/01/2020
ms.author: juliako
ms.openlocfilehash: e9855a02a57d71793f3a5d6c5ce01cbfda341b70
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632215"
---
<!-- If you update limits in this topic, make sure to also update https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#media-services-limits -->
# <a name="azure-media-services-quotas-and-limits"></a>Kvoter och begränsningar för Azure Media Services

I den här artikeln visas några av de vanligaste Microsoft Azure Media Services-gränserna, som ibland också kallas kvoter.

> [!NOTE]
> Öppna en supportbiljett för att be om en ökning av kvoterna för resurser som inte är fasta. Skapa inte ytterligare Azure Media Services-konton i ett försök att få högre gränser.

## <a name="account-limits"></a>Kontogränser

| Resurs | Standardgräns | 
| --- | --- | 
| [Media Services-konton](media-services-account-concept.md) i en enda prenumeration | 25 (fast) |

## <a name="asset-limits"></a>Tillgångsgränser

| Resurs | Standardgräns | 
| --- | --- | 
| [Tillgångar](assets-concept.md) per mediatjänstkonto | 1,000,000|

## <a name="storage-limits"></a>Begränsningar för lagring

| Resurs | Standardgräns | 
| --- | --- | 
| Filstorlek| I vissa fall finns det en gräns för den maximala filstorleken som stöds för bearbetning i Media Services. <sup>(1)</sup> |
| [Lagringskonton](storage-account-concept.md) | 100<sup>(2)</sup> (fast) |

<sup>1</sup> Den maximala storleken som stöds för en enda blob är för närvarande upp till 5 TB i Azure Blob Storage. Ytterligare begränsningar gäller i Media Services baserat på de vm-storlekar som används av tjänsten. Storleksgränsen gäller för de filer som du laddar upp och även de filer som genereras som ett resultat av Media Services-bearbetning (kodning eller analys). Om källfilen är större än 260 GB misslyckas det troligen med jobbet. 

I följande tabell visas gränserna för de mediereserverade enheterna S1, S2 och S3. Om källfilen är större än de gränser som anges i tabellen misslyckas kodningsjobbet. Om du kodar 4K-upplösningskällor med lång varaktighet måste du använda S3-mediereserverade enheter för att uppnå den prestanda som behövs. Om du har 4K-innehåll som är större än gränsen på 260 GB för S3-mediereserverade enheter öppnar du en supportbiljett.

|Mediereserverad enhetstyp|Maximal inmatningsstorlek (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> Lagringskontona måste komma från samma Azure-prenumeration.

## <a name="jobs-encoding--analyzing-limits"></a>Jobb (kodning & analysera) gränser

| Resurs | Standardgräns | 
| --- | --- | 
| [Jobb](transforms-jobs-concept.md) per mediatjänstkonto | 500 000 <sup>(3)</sup> (fast)|
| Jobbindata per jobb | 50 (fast)|
| Jobbutdata per jobb | 20 (fast) |
| [Omvandlar](transforms-jobs-concept.md) per Media Services-konto | 100 (fast)|
| Omforma utdata i en transformering | 20 (fast) |
| Filer per jobbinmatning|10 (fast)|

<sup>3</sup> Det här numret inkluderar köade, färdiga, aktiva och avbrutna jobb. Den innehåller inte borttagna jobb. 

Alla jobbposter i ditt konto som är äldre än 90 dagar tas automatiskt bort, även om det totala antalet poster ligger under den maximala kvoten. 

## <a name="live-streaming-limits"></a>Gränser för livestreaming

| Resurs | Standardgräns | 
| --- | --- | 
| [Live-evenemang](live-events-outputs-concept.md) <sup>(4)</sup> per Media Services-konto |5|
| Live-utdata per livehändelse |3 <sup>(5)</sup> |
| Max varaktighet för liveutdata | 25 timmar |

<sup>4</sup> Detaljerad information om begränsningar för livehändelse finns i [Jämförelse och gränser för livehändelsetyper](live-event-types-comparison.md).

<sup>5</sup> Live-utdata börjar skapas och stoppas när de tas bort.

## <a name="packaging--delivery-limits"></a>Förpackning & leveransgränser

| Resurs | Standardgräns | 
| --- | --- | 
| [Strömmande slutpunkter (stoppas](streaming-endpoint-concept.md) eller körs) per Media Services-konto|2 |
| [Filter för dynamiskt manifest](filters-dynamic-manifest-overview.md)|100|
| [Principer för direktuppspelning](streaming-policy-concept.md) | 100 <sup>(6)</sup> |
| Unika [streamingpositionerare](streaming-locators-concept.md) som är associerade med en tillgång samtidigt | 100<sup>(7)</sup> (fast) |

<sup>6</sup> När du använder en anpassad [streamingprincip](https://docs.microsoft.com/rest/api/media/streamingpolicies)bör du utforma en begränsad uppsättning sådana principer för ditt Media Service-konto och återanvända dem för dina StreamingLocators när samma krypteringsalternativ och protokoll behövs. Du bör inte skapa en ny strömningsprincip för varje positionerare för direktuppspelning.

<sup>7</sup> Streaming Locators är inte utformade för att hantera åtkomstkontroll per användare. Om du vill ge olika åtkomsträttigheter till enskilda användare kan du använda DRM-lösningar (Digital Rights Management).

## <a name="protection-limits"></a>Skyddsgränser

| Resurs | Standardgräns | 
| --- | --- | 
| Alternativ per [innehållsnyckelpolicy](content-key-policy-concept.md) |30 | 
| Licenser per månad för var och en av DRM-typerna på Media Services nyckelleveranstjänst per konto|1,000,000|

## <a name="support-ticket"></a>Supportbiljett

För resurser som inte är fasta kan du begära att kvoterna höjs genom att öppna en [supportbiljett.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Inkludera detaljerad information i begäran om önskade kvotändringar, användningsfallsscenarier och regioner som krävs. <br/>Försök **inte** få högre gränser genom att skapa ytterligare Azure Media Services-konton.

## <a name="next-steps"></a>Nästa steg

[Översikt](media-services-overview.md)
