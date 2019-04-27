---
title: Azure ytterdörren Service - arkitektur för Routning | Microsoft Docs
description: Den här artikeln hjälper dig att förstå global vy aspekt av Front dörren arkitektur.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 6af5e7c7d8788dffa8f144b2ee77c291ceda86c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736298"
---
# <a name="routing-architecture-overview"></a>Routning översikt över arkitekturen

Tjänsten Azure ytterdörren när den får klienten begär sedan den antingen svar dem (om cachelagring är aktiverat) eller vidarebefordrar dem till rätt program-serverdel (som en omvänd proxy).

</br>Det finns möjligheter att optimera trafiken när routning till Azure ytterdörren, samt när routning till serverdelen.

## <a name = "anycast"></a>Att välja ytterdörren miljön för trafiken dirigeras (Anycast)

Routning till Azure ytterdörren miljöer utnyttjar [Anycast](https://en.wikipedia.org/wiki/Anycast) för både DNS (Domain Name System) och HTTP (Hypertext Transfer Protocol)-trafik, så användartrafik skickas till den närmaste miljön när det gäller nätverkstopologi (antal hopp). Den här arkitekturen ger vanligtvis bättre fram och åter gånger för slutanvändare (maximera fördelarna med att dela TCP). Ytterdörren organiserar dess miljöer till primära och återställningsplats ”ringar”.  Den yttersta ringen har miljöer som är närmast användarna, erbjuda kortare svarstider.  Den inre ringen har miljöer som kan hantera redundans för den yttersta ringen miljön om ett problem inträffar. Den yttersta ringen är det prioriterade målet för all trafik, men den inre ringen krävs för att hantera trafik spill från den yttersta ringen. När det gäller VIP (virtuell IP-adresser) tilldelas varje klientdel värd eller domän som hanteras av ytterdörren en primär VIP som har meddelats av miljöer i både den inre och yttre ringen, samt en återställningsplats VIP som endast har meddelats av miljöer i den inre ringen. 

</br>Den här övergripande strategin säkerställer att begäranden från användarna alltid nå den närmaste ytterdörr-miljön och att även om den prioriterade ytterdörren environment är skadad sedan trafiken flyttas automatiskt till nästa närmaste miljö.

## <a name = "splittcp"></a>Ansluta till ytterdörren miljö (dela TCP)

[Dela TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) är en teknik för att minska fördröjningar och TCP-problem genom att bryta ned en anslutning som skulle medföra en hög fram och åter tid i mindre delar.  Genom att placera de ytterdörr miljöerna närmare för slutanvändare och avslutar TCP-anslutningar i miljön ytterdörren, är en TCP-anslutning med en stor fram och åter tid till serverdelen för programmet uppdelat i två TCP-anslutningar. Kort anslutningen mellan användaren och ytterdörren miljön innebär hämtar ansluta över tre kort tur och RETUR i stället för tre lång kommunikationsturer, sparar svarstid.  Lång anslutningen mellan ytterdörren miljön och serverdelen kan fastställas före och återanvändas i flera slutanvändarens-anrop, sparar TCP-anslutningstiden igen.  Effekten multipliceras när en anslutning för SSL/TLS (Transport Layer Security) som det finns flera sändningar anslutning.

## <a name="processing-request-to-match-a-routing-rule"></a>Bearbetning av begäran så att den matchar en regel för vidarebefordran
När du upprättar en anslutning och gör en SSL är-handskakningen när en begäran landar på en ytterdörren miljö som matchar en regel för vidarebefordran det första steget. Den här matchning i princip är att bestämma från alla konfigurationer framför dörren, vilka viss routning regel för att matcha begäran till. Läs om hur ytterdörren fungerar [väg matchar](front-door-route-matching.md) vill veta mer.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identifiera tillgängliga serverdelar i serverdelspoolen för en routningsregel
När åtkomsten har en motsvarighet för en regel för vidarebefordran av baserat på en inkommande begäran och om det finns ingen cachelagring, är nästa steg att hämta hälsostatus för avsökningen för serverdelspoolen som är associerade med den matchade vägen. Läs om hur ytterdörren övervakar serverdel hälsa med [Hälsoavsökningar](front-door-health-probes.md) vill veta mer.

## <a name="forwarding-the-request-to-your-application-backend"></a>Vidarebefordra begäran till din program-serverdel
Slutligen, förutsatt att det finns ingen cachelagring konfigurerats, användarens begäran vidarebefordras till ”bästa” serverdelen baserat på din [ytterdörren routningsmetod](front-door-routing-methods.md) konfiguration.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
