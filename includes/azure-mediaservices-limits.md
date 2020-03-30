---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2fe091a4ff0295ecadfd69ba3f2d4ca59e9612e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334860"
---
>[!NOTE]
>Öppna en supportbiljett för att be om en ökning av kvoterna för resurser som inte är fasta. Skapa inte ytterligare Azure Media Services-konton i ett försök att få högre gränser.

| Resurs | Gräns | 
| --- | --- | 
| Azure Media Services-konton i en enda prenumeration | 25 (fast) |
| Mediereserverade enheter per Media Services-konto |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Jobb per mediatjänstkonto | 50 000<sup>2</sup> |
| Länkade uppgifter per jobb | 30 (fast) |
| Tillgångar per mediatjänstkonto | 1,000,000|
| Tillgångar per uppgift | 50 |
| Tillgångar per jobb | 100 |
| Unik positionerare som är associerad med en tillgång vid ett tillfälle | 5<sup>4</sup> |
| Livekanaler per Media Services-konto |5|
| Program i stoppat tillstånd per kanal |50|
| Program i körningstillstånd per kanal |3|
| Strömmande slutpunkter som stoppas eller körs per Media Services-konto|2|
| Strömningsenheter per slutpunkt för direktuppspelning |10 |
| Lagringskonton | 1 000<sup>5</sup> (fast) |
| Principer | 1 000 000<sup>6</sup> |
| Filstorlek| I vissa fall finns det en gräns för den maximala filstorleken som stöds för bearbetning i Media Services. <sup>7.</sup> |

<sup>1.</sup> Om du ändrar typ, till exempel från S2 till S1, återställs de maximala reserverade enhetsgränserna.

<sup>2.</sup> Det här numret inkluderar köade, färdiga, aktiva och avbrutna jobb. Den innehåller inte borttagna jobb. Du kan ta bort gamla jobb med **IJob.Delete** eller **DELETE HTTP-begäran.**

Från och med den 1 april 2017 tas alla jobbposter i ditt konto som är äldre än 90 dagar bort automatiskt, tillsammans med tillhörande uppgiftsposter. Automatisk borttagning sker även om det totala antalet poster ligger under den maximala kvoten. Om du vill arkivera jobb- och uppgiftsinformationen använder du koden som beskrivs i [Hantera tillgångar med Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3.</sup> När du gör en begäran om att lista jobbentiteter returneras högst 1 000 jobb per begäran. Om du vill hålla reda på alla inskickade jobb använder du de vanligaste frågorna eller hoppar över frågor enligt beskrivningen i [OData-systemfrågaalternativ](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4.</sup> Lokaliseringsorgan är inte utformade för att hantera åtkomstkontroll per användare. Använd DRM-lösningar (Digital Rights Management) för att ge enskilda användare olika åtkomsträttigheter. Mer information finns i [Skydda ditt innehåll med Azure Media Services](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5 5</sup> Lagringskontona måste komma från samma Azure-prenumeration.

<sup>6.</sup> Det finns en gräns på 1 000 000 principer för olika Media Services-principer. Ett exempel är för positionerarprincipen eller ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Om du alltid använder samma dagar och åtkomstbehörigheter använder du samma princip-ID. Information och ett exempel finns i [Hantera tillgångar med Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7.</sup> Den maximala storleken som stöds för en enda blob är för närvarande upp till 5 TB i Azure Blob Storage. Ytterligare begränsningar gäller i Media Services baserat på de vm-storlekar som används av tjänsten. Storleksgränsen gäller för de filer som du laddar upp och även de filer som genereras som ett resultat av Media Services-bearbetning (kodning eller analys). Om källfilen är större än 260 GB misslyckas det troligen med jobbet. 

I följande tabell visas gränserna för de mediereserverade enheterna S1, S2 och S3. Om källfilen är större än de gränser som anges i tabellen misslyckas kodningsjobbet. Om du kodar 4K-upplösningskällor med lång varaktighet måste du använda S3-mediereserverade enheter för att uppnå den prestanda som behövs. Om du har 4K-innehåll som är större än gränsen på 260 GB för S3-mediereserverade enheter öppnar du en supportbiljett.

|Mediereserverad enhetstyp    |Maximal inmatningsstorlek (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|
