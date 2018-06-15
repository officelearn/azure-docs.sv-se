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
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 21fc80d7cb274197ae75d2fd5524e76e1e6288d9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788433"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Kvoter och begränsningar i Azure Media Services v3

Det här avsnittet beskrivs kvoter och begränsningar i Azure Media Services v3.

| Resurs | Standardgräns | 
| --- | --- | 
| Tillgångar per Azure Media Services-konto | 1,000,000|
| JobInputs per jobb | 100 |
| JobOutputs per jobb | 30 (fast) |
| Filstorlek| I vissa situationer kan finns det en gräns på den största filstorleken som stöds för bearbetning i Media Services. <sup>(1)</sup> |
| Jobb per Media Services-konto | 50,000<sup>(2)</sup> |
| LiveEvents per Media Services-konto |5|
| Media Services-konton i en enda prenumeration | 25 (fast) |
| StreamingPolicies | 1 000 000<sup>(3)</sup> |
| LiveOutputs körs per LiveEvent |3|
| LiveOutputs i ett stoppat tillstånd per LiveEvent |50|
| Lagringskonton | 1 000<sup>(4)</sup> (fast) |
| Strömningsslutpunkter körs per Media Services-konto|2|
| Transformeringar per Media Services-konto | 20 |
| Unik StreamingLocators som är kopplade till en resurs i taget | 20<sup>(5)</sup> |
  
<sup>1</sup>den maximala storleken som stöds för en enda blob är för närvarande upp till 5 TB i Azure Blob Storage. Dock gäller ytterligare begränsningar i Azure Media Services baserat på storlek på Virtuella datorer som används av tjänsten. Om din källfil är större än 260 GB jobbet kommer sannolikt att misslyckas. Om du har 4K-innehåll som är större än gränsen på 260 GB, kontaktar du oss på amshelp@microsoft.com för eventuella åtgärder som stöd för ditt scenario.

<sup>2</sup> antalet inkluderar köade, klar, aktiva och avbrutna jobb. Det tar inte bort jobb. 

Alla jobb poster i ditt konto som är äldre än 90 dagar tas automatiskt bort även om det totala antalet poster som är lägre än den maximala kvoten. 

<sup>3</sup> det finns en gräns på 1 000 000 StreamingPolicy poster för olika Media Services-principer (till exempel för StreamingLocator principen eller ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Du bör använda samma princip-ID om du alltid använder samma dagar, åtkomstbehörigheter etc. 

<sup>4</sup> storage-konton måste komma från samma Azure-prenumerationen.

<sup>5</sup> StreamingLocators inte har utformats för att hantera åtkomstkontroll per användare. Om du vill ge olika åtkomsträttigheter till enskilda användare kan du använda DRM-lösningar (Digital Rights Management).

## <a name="support-ticket"></a>Supportärende

För resurser som inte har åtgärdats, kan du begära kvoter höjas genom att öppna en [stöder biljett](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Ange detaljerad information i begäran för önskad kvoten ändringar, användningsfall scenarier och regioner som krävs. <br/>Försök **inte** få högre gränser genom att skapa ytterligare Azure Media Services-konton.

## <a name="next-steps"></a>Nästa steg

[Översikt](media-services-overview.md)
