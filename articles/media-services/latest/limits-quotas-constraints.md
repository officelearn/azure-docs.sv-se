---
title: Kvoter och begränsningar i Azure Media Services v3 | Microsoft-dokument
description: I det här avsnittet beskrivs kvoter och begränsningar i Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: 5a4f7e31cb17f47e8796ab99c1f8a089339903df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888435"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Kvoter och begränsningar i Azure Media Services v3

I den här artikeln beskrivs kvoter och begränsningar i Azure Media Services v3.

| Resurs | Standardgräns | 
| --- | --- | 
| Tillgångar per Azure Media Services-konto | 1,000,000|
| Filter för dynamiskt manifest|100|
| Jobbinputs per jobb | 50 (fast)|
| Jobbutsedlar per jobb | 20 (fast) |
| TransformOutputs i en transformering | 20 (fast) |
| Filer per Jobbinput|10 (fast)|
| Filstorlek| I vissa fall finns det en gräns för den maximala filstorleken som stöds för bearbetning i Media Services. <sup>(1)</sup> |
| Jobb per mediatjänstkonto | 500 000 <sup>(2)</sup> (fast)|
| Livehändelser per Media Services-konto |5|
| Media Services-konton i en enda prenumeration | 25 (fast) |
| Live-utdata per livehändelse |3 <sup>(3)</sup> |
| Max varaktighet för liveutdata | 25 timmar |
| Lagringskonton | 100<sup>(4)</sup> (fast) |
| Strömmande slutpunkter (stoppas eller körs) per Media Services-konto|2 (fast)|
| Principer för direktuppspelning | 100 <sup>(5)</sup> |
| Omvandlar per Media Services-konto | 100 (fast)|
| Unika streamingpositionerare som är associerade med en tillgång samtidigt | 100<sup>(6)</sup> (fast) |
| Alternativ per innehållsnyckelpolicy |30 | 
| Licenser per månad för var och en av DRM-typerna på Media Services nyckelleveranstjänst per konto|1,000,000|

<sup>1</sup> Den maximala storleken som stöds för en enda blob är för närvarande upp till 5 TB i Azure Blob Storage. Ytterligare begränsningar gäller i Media Services baserat på de vm-storlekar som används av tjänsten. Storleksgränsen gäller för de filer som du laddar upp och även de filer som genereras som ett resultat av Media Services-bearbetning (kodning eller analys). Om källfilen är större än 260 GB misslyckas det troligen med jobbet. 

I följande tabell visas gränserna för de mediereserverade enheterna S1, S2 och S3. Om källfilen är större än de gränser som anges i tabellen misslyckas kodningsjobbet. Om du kodar 4K-upplösningskällor med lång varaktighet måste du använda S3-mediereserverade enheter för att uppnå den prestanda som behövs. Om du har 4K-innehåll som är större än gränsen på 260 GB för S3-mediereserverade enheter öppnar du en supportbiljett.

|Mediereserverad enhetstyp   |Maximal inmatningsstorlek (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> Det här numret inkluderar köade, färdiga, aktiva och avbrutna jobb. Den innehåller inte borttagna jobb. 

Alla jobbposter i ditt konto som är äldre än 90 dagar tas automatiskt bort, även om det totala antalet poster ligger under den maximala kvoten. 

<sup>3</sup> Live-utdata börjar skapas och stoppas när de tas bort.

<sup>4</sup> Lagringskontona måste komma från samma Azure-prenumeration.

<sup>5</sup> När du använder en anpassad [streamingprincip](https://docs.microsoft.com/rest/api/media/streamingpolicies)bör du utforma en begränsad uppsättning sådana principer för ditt Media Service-konto och återanvända dem för dina StreamingLocators när samma krypteringsalternativ och protokoll behövs. Du bör inte skapa en ny strömningsprincip för varje positionerare för direktuppspelning.

<sup>6</sup> Streaming Locators är inte utformade för att hantera åtkomstkontroll per användare. Om du vill ge olika åtkomsträttigheter till enskilda användare kan du använda DRM-lösningar (Digital Rights Management).

## <a name="support-ticket"></a>Supportbiljett

För resurser som inte är fasta kan du begära att kvoterna höjs genom att öppna en [supportbiljett.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Inkludera detaljerad information i begäran om önskade kvotändringar, användningsfallsscenarier och regioner som krävs. <br/>Försök **inte** få högre gränser genom att skapa ytterligare Azure Media Services-konton.

## <a name="next-steps"></a>Nästa steg

[Översikt](media-services-overview.md)
