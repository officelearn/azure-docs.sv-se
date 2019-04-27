---
title: Protokoll som stöds för HTTP-huvuden i Azure ytterdörren Service | Microsoft Docs
description: Den här artikeln beskriver HTTP-huvud protokoll som stöds av ytterdörren Service.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 92e8435e4336c68982e4becc2a95f99b2c776c0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736656"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door-service"></a>Protokollstöd för HTTP-huvuden i Azure ytterdörren Service
Den här artikeln beskriver det protokoll som ytterdörren tjänsten stöder med delar av anropet sökvägen (se bild). Följande avsnitt innehåller mer information om HTTP-huvuden som stöds av ytterdörren-tjänsten.

![Azure ytterdörren Service HTTP-huvuden-protokoll][1]

>[!IMPORTANT]
>Ytterdörren Service certifiera inte alla HTTP-huvuden som inte beskrivs här.

## <a name="client-to-front-door-service"></a>Klient till ytterdörren-tjänsten
Ytterdörren Service accepterar de flesta meddelandehuvuden från den inkommande begäranden utan att ändra dem. Vissa reserverade huvuden tas bort från den inkommande begäran om skickas, inklusive rubriker med X - FD-* prefix.

## <a name="front-door-service-to-backend"></a>Ytterdörren Service till serverdelen

Ytterdörren Service innehåller rubriker från en inkommande begäran om inte bort på grund av begränsningar. Ytterdörren lägger även till följande rubriker:

| Huvud  | Exempel och beskrivning |
| ------------- | ------------- |
| Via |  Via: 1.1 azure </br> Ytterdörren lägger till klientens HTTP-version följt av *Azure* som värde för sidhuvudet Via. Detta anger klientens HTTP-version och den åtkomsten har en mellanliggande mottagare för förfrågan mellan klienten och serverdelen.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Representerar klientens IP-adress som är associerade med begäran bearbetas. En begäran kommer från en proxy kan till exempel lägga till rubriken X-vidarebefordrade-för att ange IP-adressen för den ursprungliga anroparen. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Representerar socket-IP-adress som är associerad med TCP-anslutningen som den aktuella begäran kommer från. En begäran klientens IP-adress kanske inte är lika med dess socket IP-adress eftersom godtyckligt kan åsidosättas av en användare.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> En unik referens-sträng som identifierar en begäran som hanteras av ytterdörren. Används för att söka åtkomst loggarna och kritiska för felsökning.|
| X-Azure-RequestChain |  X-Azure-RequestChain: hops=1 </br> Ett huvud att åtkomsten för att identifiera begäran loopar och användare som inte ska ett beroende på den. |
| X-Forwarded-For | X-Forwarded-For: 127.0.0.1 </br> Fältet X-Forwarded-For (XFF) HTTP-huvud identifierar ofta den ursprungliga IP-adressen för en klient som ansluter till en server via en HTTP-proxy eller belastningsutjämnare. Om det finns en befintlig XFF rubrik, ytterdörren lägger till klientens socket IP-Adressen eller lägger till rubriken XFF med klientens socket IP-Adressen. |
| X-Forwarded-Host | X-vidarebefordrade-värd: contoso.azurefd.net </br> Fältet X-vidarebefordrade-värd HTTP-huvud är en vanlig metod som används för att identifiera den ursprungliga värden som begärs av klienten i värden http-rubriken. Det beror på att du värdnamnet från ytterdörren kan skilja sig för backend-servern som hanterar begäran. |
| X-Forwarded-Proto | X-Forwarded-Proto: http </br> Fältet för X-vidarebefordrade-Proto HTTP-huvud används ofta för att identifiera det ursprungliga protokollet för en HTTP-begäran eftersom åtkomsten baserat på konfigurationen, kan kommunicera med serverdelen med hjälp av HTTPS. Detta gäller även om förfrågan om att den omvända proxyn är HTTP. |

## <a name="front-door-service-to-client"></a>Ytterdörren Service till klienten

Rubriker som skickas till ytterdörren från serverdelen skickas också till klienten. Här följer huvuden som skickas från åtkomsten till klienter.

| Huvud  | Exempel |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Det här är en unik referens-sträng som identifierar en begäran som hanteras av ytterdörren. Detta är viktigt för att felsöka som används för att söka åtkomst loggarna.|

## <a name="next-steps"></a>Nästa steg

- [Skapa en ytterdörren](quickstart-create-front-door.md)
- [Hur ytterdörren fungerar](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png