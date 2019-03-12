---
title: Azure ytterdörren Service – stöd för HTTP-huvuden protokoll | Microsoft Docs
description: Den här artikeln hjälper dig att förstå de HTTP-huvud protokoll som stöds av ytterdörren
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
ms.openlocfilehash: b34ab417ab1d9ef77c3141d5aa130c338fb89188
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726336"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Azure ytterdörren Service – stöd för HTTP-huvuden protokoll
Det här dokumentet beskriver det protokoll som Azure ytterdörren tjänsten stöder med de olika delarna av anropet sökvägen från bilden nedan. I avsnitten nedan ger mer information om HTTP-huvuden som har stöd för åtkomsten.

![Azure ytterdörren Service HTTP-huvuden-protokoll][1]

>[!WARNING]
>Azure ytterdörren-tjänst ger inga garantier på alla HTTP-huvuden som inte finns dokumenterade här.

## <a name="1-client-to-front-door"></a>1. Klient till ytterdörren
Ytterdörren accepterar de flesta meddelandehuvuden från den inkommande begäranden (utan att ändra dem), men det finns vissa reserverade huvuden som tas bort från den inkommande begäranden om de skickas. Detta inkluderar rubriker med följande prefix:
 - X-FD-*

## <a name="2-front-door-to-backend"></a>2. Åtkomsten till serverdelen

Ytterdörren innehåller rubrikerna från den inkommande begäran, såvida inte de har tagits bort på grund av begränsningar som nämns ovan. Ytterdörren kommer också att lägga till följande rubriker:

| Huvud  | Exempel och beskrivning |
| ------------- | ------------- |
| Via |  *Via: 1.1 azure* </br> Ytterdörren lägger till klientens HTTP-version följt av ”Azure” som värde för Via rubrik. Detta har lagts till för att ange klientens HTTP-version och den Azure-ytterdörren var en mellanliggande mottagare för förfrågan mellan klienten och serverdelen.  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> Representerar ”klient”-IP-adress som är associerade med begäran bearbetas. En begäran kommer från en proxy kan till exempel lägga till rubriken X-vidarebefordrade-för att ange IP-adressen för den ursprungliga anroparen. |
| X-Azure-SocketIP |  *X-Azure-SocketIP: 127.0.0.1* </br> Representerar socket-IP-adress som är associerade med den aktuella begäran har sitt ursprung på den TCP-anslutningen. En begäran klient-IP-adress kanske inte lika med dess Socket IP-adress eftersom godtyckligt kan åsidosättas av en slutanvändare.|
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Det här är en unik referens-sträng som identifierar en begäran som hanteras av ytterdörren. Det är viktigt för att felsöka som används för att söka i Åtkomstloggarna.|
| X-Azure-RequestChain |  *X-Azure-RequestChain: hops=1* </br> Detta är en rubrik som ytterdörren använder för att identifiera begäran loopar och användare som inte ska ett beroende på den. |
| X-Forwarded-For | *X-Forwarded-For: 127.0.0.1* </br> Huvudfältet X-Forwarded-For (XFF) HTTP-är en vanlig metod för att identifiera den ursprungliga IP-adressen för en klient som ansluter till en server via en HTTP-proxy eller belastningsutjämnare. Om det har en befintlig XFF rubrik och sedan ytterdörren lägger till klientens socket IP-Adressen till den andra lägger du till rubriken XFF med klientens socket IP-Adressen. |
| X-Forwarded-Host | *X-vidarebefordrade-värd: contoso.azurefd.net* </br> Fältet X-vidarebefordrade-värd HTTP-huvud är en vanlig metod för att identifiera den ursprungliga värden som begärs av klienten i värden http-rubriken, eftersom du värdnamnet från ytterdörren kan skilja sig för backend-servern som hanterar begäran. |
| X-Forwarded-Proto | *X-Forwarded-Proto: http* </br> Fältet X vidarebefordras Proto HTTP-huvud är en vanlig metod för att identifiera det ursprungliga protokollet för en HTTP-begäran eftersom beroende på hur ytterdörren kan kommunicera med serverdelen med HTTPS, även om förfrågan om att den omvända proxyn är HTTP. |

## <a name="3-front-door-to-client"></a>3. Åtkomsten till klienten

Följande är de rubriker som skickas från åtkomsten till klienter. Rubriker som skickas till ytterdörren från serverdelen skickas till klienten även.

| Huvud  | Exempel |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Det här är en unik referens-sträng som identifierar en begäran som hanteras av ytterdörren. Det är viktigt för att felsöka som används för att söka i Åtkomstloggarna.|

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png