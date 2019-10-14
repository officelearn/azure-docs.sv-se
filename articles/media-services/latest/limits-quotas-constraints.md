---
title: Kvoter och begränsningar i Azure Media Services v3 | Microsoft Docs
description: I det här avsnittet beskrivs kvoter och begränsningar i Azure Media Services v3
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/11/2019
ms.author: juliako
ms.openlocfilehash: 819548d784e5cba9fcec6b2110137d91bf28e03d
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296920"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Kvoter och begränsningar i Azure Media Services v3

I den här artikeln beskrivs kvoter och begränsningar i Azure Media Services v3.

| Resurs | Standardgräns | 
| --- | --- | 
| Tillgångar per Azure Media Services-konto | 1,000,000|
| Filter för dynamiskt manifest|100|
| JobInputs per jobb | 50 (fast)|
| JobOutputs per jobb | 20 (fast) |
| TransformOutputs i en transformering | 20 (fast) |
| Filer per JobInput|10 (fast)|
| Filstorlek| I vissa fall finns det en gräns för maximal fil storlek som stöds för bearbetning i Media Services. <sup>81.1</sup> |
| Jobb per Media Services konto | 500 000 <sup>(2)</sup> (fast)|
| Livehändelser per Media Services-konto |5|
| Media Services konton i en enda prenumeration | 25 (fast) |
| Live-utdata per Live-händelse |3 <sup>(3)</sup> |
| Maximal varaktighet för Live-utdata | 25 timmar |
| Lagringskonton | 100<sup>(4)</sup> (fast) |
| Slut punkter för direkt uppspelning (stoppad eller igång) per Media Services konto|2 (fast)|
| Principer för direktuppspelning | 100 <sup>(5)</sup> |
| Transformeringar per Media Services konto | 100 (fast)|
| Unika strömmande positionerare som är associerade med en till gång vid ett tillfälle | 100<sup>(6)</sup> (fast) |
| Alternativ per innehålls nyckel princip |30 | 
| Licenser per månad för varje DRM-typ på Media Services Key Delivery Service per konto|1,000,000|

<sup>1</sup> den maximala storleken som stöds för en enskild BLOB är för närvarande upp till 5 TB i Azure Blob Storage. Ytterligare gränser gäller i Media Services baserat på de VM-storlekar som används av tjänsten. Storleks gränsen gäller för de filer som du överför och även de filer som genereras på grund av Media Services bearbetning (kodning eller analys). Om käll filen är större än 260 GB kommer jobbet sannolikt att Miss förväntas. 

I följande tabell visas gränserna för de medie reserverade enheterna S1, S2 och S3. Om käll filen är större än de gränser som anges i tabellen, Miss lyckas kodnings jobbet. Om du kodar 4K-resolutioner med lång varaktighet måste du använda S3-medie reserverade enheter för att uppnå de prestanda som behövs. Om du har ett 4K-innehåll som är större än 260 GB-gränsen på S3-enheter kan du kontakta oss på amshelp@microsoft.com för potentiella åtgärder som stöder ditt scenario.

|Enhets typ för reserverat medium   |Maximal ingångs storlek (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> det här talet omfattar köade, avslutade, aktiva och avbrutna jobb. Den inkluderar inte borttagna jobb. 

Eventuella jobb poster i ditt konto som är äldre än 90 dagar tas bort automatiskt, även om det totala antalet poster unders tiger den maximala kvoten. 

<sup>3</sup> Live-utdata börjar skapas och stoppas när de tas bort.

<sup>4</sup> lagrings kontona måste vara från samma Azure-prenumeration.

<sup>5</sup> när du använder en anpassad [strömnings princip](https://docs.microsoft.com/rest/api/media/streamingpolicies)bör du utforma en begränsad uppsättning sådana principer för ditt Media Service-konto och sedan använda dem igen för din StreamingLocators när samma krypterings alternativ och protokoll behövs. Du bör inte skapa en ny strömningsprincip för varje positionerare för direktuppspelning.

<sup>6</sup> strömmande positionerare är inte utformade för att hantera åtkomst kontroll per användare. Om du vill ge olika åtkomsträttigheter till enskilda användare kan du använda DRM-lösningar (Digital Rights Management).

## <a name="support-ticket"></a>Support ärende

För resurser som inte har åtgärd ATS kan du be att kvoterna höjs, genom att öppna ett [support ärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Ta med detaljerad information i begäran om önskade kvot ändringar, användnings Falls scenarier och regioner som krävs. <br/>Försök **inte** få högre gränser genom att skapa ytterligare Azure Media Services-konton.

## <a name="next-steps"></a>Nästa steg

[Översikt](media-services-overview.md)
