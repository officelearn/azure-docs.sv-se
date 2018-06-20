---
title: Kvoter och begränsningar i Azure Media Services v3 | Microsoft Docs
description: Det här avsnittet beskrivs kvoter och begränsningar i Azure Media Services v3
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/13/2018
ms.author: juliako
ms.openlocfilehash: 75bfb0d5d29f0b8e038e68af08130564b72a05bf
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266761"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Kvoter och begränsningar i Azure Media Services v3

Den här artikeln beskrivs kvoter och begränsningar i Azure Media Services v3.

| Resurs | Standardgräns | 
| --- | --- | 
| Tillgångar per Azure Media Services-konto | 1,000,000|
| JobInputs per jobb | 50 |
| JobOutputs per jobb | 20 (fast) |
| Filstorlek| I vissa situationer kan finns det en gräns på den största filstorleken som stöds för bearbetning i Media Services. <sup>(1)</sup> |
| Jobb per Media Services-konto | 500 000 <sup>(2)</sup>|
| Visar en lista över transformeringar|Sidbryta svar med 1000 transformeringar per sida|
| Visar en lista över jobb|Sidbryta svar med 500 jobb per sida|
| LiveEvents per Media Services-konto |5|
| Media Services-konton i en enda prenumeration | 25 (fast) |
| StreamingPolicies | 1 000 000<sup>(3)</sup> |
| LiveOutputs körs per LiveEvent |3|
| LiveOutputs i ett stoppat tillstånd per LiveEvent |50|
| Antalet filer per JobInput|10|
| Lagringskonton | 100<sup>(4)</sup> (fast) |
| Strömningsslutpunkter körs per Media Services-konto|2|
| Transformeringar per Media Services-konto | 100 |
| TransformOutputs i en transformering| 20|
| Unik StreamingLocators som är kopplade till en resurs i taget | 20<sup>(5)</sup> |

<sup>1</sup> den maximala storleken som stöds för en enda blob är för närvarande upp till 5 TB i Azure Blob Storage. Dock gäller ytterligare begränsningar i Azure Media Services baserat på storlek på Virtuella datorer som används av tjänsten. Om din källfil är större än 260 GB, misslyckas sannolikt ditt jobb. Om du har 4K-innehåll som är större än gränsen på 260 GB, kontaktar du oss på amshelp@microsoft.com för eventuella åtgärder som stöd för ditt scenario.

<sup>2</sup> antalet inkluderar köade, klar, aktiva och avbrutna jobb. Det tar inte bort jobb. 

Alla jobb poster i ditt konto som är äldre än 90 dagar tas automatiskt bort även om det totala antalet poster som är lägre än den maximala kvoten. 

<sup>3</sup> när du använder en anpassad [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies), du bör utforma en begränsad uppsättning sådana principer för ditt Media Service-konto och använda dem igen för din StreamingLocators när samma krypteringen alternativ och protokoll behövs. Du bör inte skapa en ny StreamingPolicy för varje StreamingLocator.

<sup>4</sup> storage-konton måste komma från samma Azure-prenumerationen.

<sup>5</sup> StreamingLocators inte har utformats för att hantera åtkomstkontroll per användare. Om du vill ge olika åtkomsträttigheter till enskilda användare kan du använda DRM-lösningar (Digital Rights Management).

## <a name="support-ticket"></a>Supportärende

För resurser som inte har åtgärdats, kan du begära kvoter höjas genom att öppna en [stöder biljett](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Innehåller detaljerad information i begäran på den önskade kvoten ändringar, användningsfall scenarier och regioner som krävs. <br/>Försök **inte** få högre gränser genom att skapa ytterligare Azure Media Services-konton.

## <a name="next-steps"></a>Nästa steg

[Översikt](media-services-overview.md)
