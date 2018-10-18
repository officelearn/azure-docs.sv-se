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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 5c0fbf396faa0e07ecca4ae16c775a39404c6fc9
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376524"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Kvoter och begränsningar i Azure Media Services v3

Den här artikeln beskrivs kvoter och begränsningar i Azure Media Services v3.

| Resurs | Standardgräns | 
| --- | --- | 
| Tillgångar per Azure Media Services-konto | 1,000,000|
| Dynamiska Manifestfilter|100|
| JobInputs per jobb | 50 (fast)|
| JobOutputs per jobb/TransformOutputs i en transformering | 20 (fast) |
| Filer per JobInput|10 (fast)|
| Filstorlek| I vissa situationer kan finns det en gräns för maximal filstorlek för bearbetning i Media Services. <sup>(1)</sup> |
| Jobb per Media Services-konto | 500 000 <sup>(2)</sup> (fast)|
| Visa en lista över transformeringar|Sidbryta i svaret med 1000 transformeringar per sida|
| Visa en lista över jobb|Sidbryta svaret med 500 jobb per sida|
| LiveEvents per Media Services-konto |5|
| Media Services-konton för en enskild prenumeration | 25 (fast) |
| LiveOutputs i körningstillstånd per LiveEvent |3|
| Lagringskonton | 100<sup>(4)</sup> (fast) |
| Slutpunkter för direktuppspelning i körningstillstånd per Media Services-konto|2|
| StreamingPolicies | 100 <sup>(3)</sup> |
| Transformeringar per Media Services-konto | 100 (fast)|
| Unika StreamingLocators som är associerade med en tillgång i taget | 100<sup>(5)</sup> (fast) |

<sup>1</sup> den maximala storleken som stöds för en enda blob är för närvarande upp till 5 TB i Azure Blob Storage. Ytterligare begränsningar gäller dock i Azure Media Services baserat på de storlekar som används av tjänsten. Om källfilen är större än 260 GB, misslyckas sannolikt ditt jobb. Om du har 4K-innehåll som är större än 260 GB gränsen kontaktar du oss på amshelp@microsoft.com för möjliga lösningar för ditt scenario.

<sup>2</sup> det här värdet innefattar köade, klar, aktiva och avbrutna jobb. Den omfattar inte borttagna jobb. 

Alla jobbposter i ditt konto som är äldre än 90 dagar tas automatiskt bort, även om det totala antalet poster är lägre än den maximala kvoten. 

<sup>3</sup> när du använder en anpassad [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies), bör du utforma en begränsad uppsättning principer för ditt Media Services-konto och återanvända dem för din StreamingLocators när samma krypteringen alternativ och protokoll krävs. Du bör inte skapa en ny StreamingPolicy för varje StreamingLocator.

<sup>4</sup> storage-konton måste komma från samma Azure-prenumeration.

<sup>5</sup> StreamingLocators är inte utformade för att hantera åtkomstkontroll per användare. Om du vill ge olika åtkomsträttigheter till enskilda användare kan du använda DRM-lösningar (Digital Rights Management).

## <a name="support-ticket"></a>Supportärende

För resurser som inte är fasta kan du be om att öka kvoterna genom att öppna en [supportärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Innehåller detaljerad information i begäran på den önskade kvotändringar, användningsfall och regioner som krävs. <br/>Försök **inte** få högre gränser genom att skapa ytterligare Azure Media Services-konton.

## <a name="next-steps"></a>Nästa steg

[Översikt](media-services-overview.md)
