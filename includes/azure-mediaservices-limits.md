---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: de3fd8dc0d45ea10e64af8e2258682a9e98639dc
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "67187724"
---
>[!NOTE]
>För resurser som inte har åtgärd ATS öppnar du ett support ärende för att be om en ökning av kvoterna. Skapa inte ytterligare Azure Media Services konton i ett försök att få högre gränser.

| Resource | Standardgräns | 
| --- | --- | 
| Azure Media Services konton i en enda prenumeration | 25 (fast) |
| Reserverade enheter per Media Services konto |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Jobb per Media Services konto | 50,000<sup>2</sup> |
| Länkade uppgifter per jobb | 30 (fast) |
| Till gångar per Media Services konto | 1,000,000|
| Tillgångar per uppgift | 50 |
| Tillgångar per jobb | 100 |
| Unik positionerare som är associerad med en tillgång vid ett tillfälle | 5<sup>4</sup> |
| Live Channels per Media Services konto |5|
| Program i stoppat tillstånd per kanal |50|
| Program i körningstillstånd per kanal |3|
| Slut punkter för direkt uppspelning som har stoppats eller körts per Media Services konto|2|
| Strömningsenheter per slutpunkt för direktuppspelning |10 |
| Lagringskonton | 1,000<sup>5</sup> (fast) |
| Principer | 1,000,000<sup>6</sup> |
| Filstorlek| I vissa fall finns det en gräns för maximal fil storlek som stöds för bearbetning i Media Services. <sup>7</sup> |

<sup>1</sup> Om du ändrar typ, till exempel från S2 till S1, återställs de högsta reserverade enhets gränserna.

<sup>2</sup> Det här antalet inkluderar köade, avslutade, aktiva och avbrutna jobb. Det inkluderar inte borttagna jobb. Du kan ta bort gamla jobb med **IJob. Delete** eller **ta bort** http-begäran.

Från och med den 1 april 2017 raderas alla jobb poster i ditt konto som är äldre än 90 dagar automatiskt, tillsammans med tillhör ande uppgifts poster. Automatisk borttagning sker även om det totala antalet poster unders tiger Max kvoten. Om du vill arkivera jobb-och uppgifts information använder du koden som beskrivs i [Hantera till gångar med Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup> När du gör en begäran om att lista jobb enheter returneras högst 1 000 jobb per begäran. Om du vill hålla koll på alla skickade jobb använder du de översta eller SKIP-frågorna som beskrivs i [alternativ för OData system-frågor](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4</sup> Lokaliserare är inte utformade för att hantera åtkomst kontroll per användare. Använd Digital Rights Management-lösningar (DRM) om du vill ge olika åtkomst rättigheter till enskilda användare. Mer information finns i [skydda ditt innehåll med Azure Media Services](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5</sup> Lagrings kontona måste vara från samma Azure-prenumeration.

<sup>6</sup> Det finns en gräns på 1 000 000-principer för olika Media Servicess principer. Ett exempel är för lokaliserings principen eller ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Om du alltid använder samma dagar och åtkomst behörigheter använder du samma princip-ID. Information och ett exempel finns i [Hantera till gångar med Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup> Den maximala storlek som stöds för en enskild BLOB är för närvarande upp till 5 TB i Azure Blob Storage. Ytterligare gränser gäller i Media Services baserat på de VM-storlekar som används av tjänsten. Storleks gränsen gäller för de filer som du överför och även de filer som genereras på grund av Media Services bearbetning (kodning eller analys). Om käll filen är större än 260 GB kommer jobbet sannolikt att Miss förväntas. 

I följande tabell visas gränserna för de medie reserverade enheterna S1, S2 och S3. Om käll filen är större än de gränser som anges i tabellen, Miss lyckas kodnings jobbet. Om du kodar 4K-resolutioner med lång varaktighet måste du använda S3-medie reserverade enheter för att uppnå de prestanda som behövs. Om du har ett 4K-innehåll som är större än 260 GB-gränsen på S3-enheter, kan du kontakta oss amshelp@microsoft.com på för att få eventuella begränsningar för att stödja ditt scenario.

|Enhets typ för reserverat medium   |Maximal ingångs storlek (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|
