---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: b275a86f8fd35c43865fd920d1bfc9994a796a9c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59805196"
---
>[!NOTE]
>Öppna ett supportärende och be om en ökning av kvoter för resurser som inte har åtgärdats. Skapa inte ytterligare Azure Media Services-konton i ett försök att få högre gränser.

| Resurs | Standardgräns | 
| --- | --- | 
| Azure Media Services-konton för en enskild prenumeration | 25 (fast) |
| Mediereserverade enheter per Media Services-konto |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Jobb per Media Services-konto | 50,000<sup>2</sup> |
| Länkade uppgifter per jobb | 30 (fast) |
| Tillgångar per Media Services-konto | 1,000,000|
| Tillgångar per uppgift | 50 |
| Tillgångar per jobb | 100 |
| Unik positionerare som är associerad med en tillgång vid ett tillfälle | 5<sup>4</sup> |
| Livekanaler per Media Services-konto |5|
| Program i stoppat tillstånd per kanal |50|
| Program i körningstillstånd per kanal |3|
| Slutpunkter för direktuppspelning som är stoppad eller som körs per Media Services-konto|2|
| Strömningsenheter per slutpunkt för direktuppspelning |10 |
| Lagringskonton | 1,000<sup>5</sup> (fast) |
| Principer | 1,000,000<sup>6</sup> |
| Filstorlek| I vissa situationer kan finns det en gräns för maximal filstorlek för bearbetning i Media Services. <sup>7</sup> |

<sup>1</sup>om du ändrar typen, till exempel från S2 till S1, begränsningar för maximal reserverad enhet återställs.

<sup>2</sup>det här värdet innefattar köade, klar, aktiva och avbrutna jobb. De omfattar inte borttagna jobb. Du kan ta bort gamla jobb med hjälp av **IJob.Delete** eller **ta bort** HTTP-begäran.

Från och med den 1 April 2017 raderas automatiskt alla jobbposter i ditt konto som är äldre än 90 dagar, tillsammans med dess associerade uppgiftsposter. Automatisk borttagning sker även om det totala antalet poster är lägre än den maximala kvoten. Om du vill arkivera informationen om jobb- och använda koden som beskrivs i [hantera tillgångar med Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md).

<sup>3</sup>när du gör en begäran att lista jobb entiteter, högst 1 000 jobben returneras per begäran. Håll koll på alla skickade jobb, Använd upp eller hoppa över frågor enligt beskrivningen i [system för OData-frågealternativ](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7)).

<sup>4</sup>positionerare är inte utformade för att hantera åtkomstkontroll per användare. Använd lösningar för digital rights management (DRM) för att ge olika åtkomsträttigheter till enskilda användare. Mer information finns i [skydda ditt innehåll med Azure Media Services](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5</sup>storage-konton måste komma från samma Azure-prenumeration.

<sup>6</sup>det finns en gräns på 1 000 000 principer för olika Media Services-principer. Det är ett exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy. 

>[!NOTE]
> Om du alltid använder samma dagar och åtkomstbehörigheter, använda samma princip-ID. Information och ett exempel finns i [hantera tillgångar med Media Services .NET SDK](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup>om du överför innehåll till en tillgång i Media Services för att bearbeta den med en av medieprocessorerna i tjänsten, notera de maximala filstorlek som stöds. Tillgångar är kodare som Media Encoder Standard och Media Encoder Premium Workflow eller analysmotorer som Face Detector.

Den maximala storleken som stöds för en enda blob är för närvarande upp till 5 TB i Azure Blob Storage. Ytterligare begränsningar gäller i Media Services baserat på de storlekar som används av tjänsten. I följande tabell visas gränserna på mediereserverade enheter S1, S2 och S3. Om källfilen är större än de gränser som definierats i tabellen, inte dina kodningsjobb. Om du kodar 4K upplösning källor för lång tid måste du använda S3 mediereserverade enheter för att uppnå prestanda som behövs. Om du har 4K-innehåll som är större än 260 GB-gränsen för S3-mediereserverade enheter kontaktar du oss på amshelp@microsoft.com för möjliga lösningar för ditt scenario.

| Mediereserverade typ av enhet | Maximal inkommande storlek (GB)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|
