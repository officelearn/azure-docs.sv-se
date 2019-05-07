---
title: Kvoter och begränsningar i Azure Media Services v3 | Microsoft Docs
description: Det här avsnittet beskriver kvoter och begränsningar i Azure Media Services v3
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/02/2019
ms.author: juliako
ms.openlocfilehash: 42b8c4caa53ffa6b3bc1148544c75602597ac452
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153831"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Kvoter och begränsningar i Azure Media Services v3

Den här artikeln beskrivs kvoter och begränsningar i Azure Media Services v3.

| Resource | Standardgräns | 
| --- | --- | 
| Tillgångar per Azure Media Services-konto | 1,000,000|
| Filter för dynamiskt manifest|100|
| JobInputs per jobb | 50 (fast)|
| JobOutputs per jobb | 20 (fast) |
| TransformOutputs i en transformering | 20 (fast) |
| Filer per JobInput|10 (fast)|
| Filstorlek| I vissa situationer kan finns det en gräns för maximal filstorlek för bearbetning i Media Services. <sup>(1)</sup> |
| Jobb per Media Services-konto | 500 000 <sup>(2)</sup> (fast)|
| Visa en lista över transformeringar|Sidbryta svaret med 1 000 transformeringar per sida|
| Visa en lista över jobb|Sidbryta svaret med 500 jobb per sida|
| Livehändelser per Media Services-konto |5|
| Media Services-konton för en enskild prenumeration | 25 (fast) |
| Live utdata per Live-händelse |3 <sup>(3)</sup> |
| Max Live utdata varaktighet | 25: e timme |
| Lagringskonton | 100<sup>(4)</sup> (fast) |
| Slutpunkter för direktuppspelning (stoppas eller köra) per Media Services-konto|2 (fast)|
| Principer för direktuppspelning | 100 <sup>(5)</sup> |
| Transformeringar per Media Services-konto | 100 (fast)|
| Unikt Streaming positionerare som är associerad med en tillgång i taget | 100<sup>(6)</sup> (fast) |
| Princip för innehållsnyckel |30 | 

<sup>1</sup> den maximala storleken som stöds för en enda blob är för närvarande upp till 5 TB i Azure Blob Storage. Ytterligare begränsningar gäller i Media Services baserat på de storlekar som används av tjänsten. Storleksgränsen gäller för de filer som du laddar upp och de filer som skapats på grund av Media Services-bearbetning (kodning eller analysera). Om källfilen är större än 260 GB, misslyckas sannolikt ditt jobb. 

I följande tabell visas gränserna på mediereserverade enheter S1, S2 och S3. Om källfilen är större än de gränser som definierats i tabellen, inte dina kodningsjobb. Om du kodar 4K upplösning källor för lång tid måste du använda S3 mediereserverade enheter för att uppnå prestanda som behövs. Om du har 4K-innehåll som är större än 260 GB-gränsen för S3-mediereserverade enheter kontaktar du oss på amshelp@microsoft.com för möjliga lösningar för ditt scenario.

|Mediereserverade typ av enhet   |Maximal inkommande storlek (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> det här värdet innefattar köade, klar, aktiva och avbrutna jobb. Den omfattar inte borttagna jobb. 

Alla jobbposter i ditt konto som är äldre än 90 dagar tas automatiskt bort, även om det totala antalet poster är lägre än den maximala kvoten. 

<sup>3</sup> live utdata börjar vid skapandet och avbryts när tas bort.

<sup>4</sup> storage-konton måste komma från samma Azure-prenumeration.

<sup>5</sup> när du använder en anpassad [Streaming princip](https://docs.microsoft.com/rest/api/media/streamingpolicies), bör du utforma en begränsad uppsättning principer för ditt Media Services-konto och återanvända dem för din StreamingLocators när samma krypteringen alternativ och protokoll krävs. Du bör inte skapa en ny strömningsprincip för varje positionerare för direktuppspelning.

<sup>6</sup> positionerare för direktuppspelning är inte utformade för att hantera åtkomstkontroll per användare. Om du vill ge olika åtkomsträttigheter till enskilda användare kan du använda DRM-lösningar (Digital Rights Management).

## <a name="support-ticket"></a>Supportärende

För resurser som inte är fasta kan du be om att öka kvoterna genom att öppna en [supportärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Innehåller detaljerad information i begäran på den önskade kvotändringar, användningsfall och regioner som krävs. <br/>Försök **inte** få högre gränser genom att skapa ytterligare Azure Media Services-konton.

## <a name="next-steps"></a>Nästa steg

[Översikt](media-services-overview.md)
