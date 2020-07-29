---
title: Arkitektur för Azures front dörr-routning | Microsoft Docs
description: Den här artikeln hjälper dig att förstå den globala vyns aspekt av den främre dörrens arkitektur.
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
ms.openlocfilehash: a088e52f742f96a13ba61969c2d7a6697c96b145
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80879300"
---
# <a name="routing-architecture-overview"></a>Översikt över arkitektur för routning

Azures front dörr när den tar emot klient begär Anden, svarar den antingen (om cachelagring är aktiverat) eller vidarebefordrar dem till lämplig program Server del (som en omvänd proxy).

</br>Det finns möjligheter att optimera trafiken vid routning till Azures front dörr samt vid routning till Server delar.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>Välja miljön för den främre dörren för trafik dirigering (anycast)

Routning till Azures frontend-miljöer utnyttjar [anycast](https://en.wikipedia.org/wiki/Anycast) för både DNS-(Domain Name System) och HTTP-trafik (Hypertext Transfer Protocol), så att användar trafiken går till den närmaste miljön vad gäller nätverk sto pol Ogin (minsta antal hopp). Den här arkitekturen erbjuder vanligt vis bättre svars tider för slutanvändare (maximera fördelarna med delade TCP). Front dörren ordnar sina miljöer i primär och återgång till "ringar".  Den yttre ringen har miljöer som är närmare användare, vilket ger lägre fördröjning.  Den inre ringen har miljöer som kan hantera redundansväxlingen för den yttre ring miljön, om ett problem uppstår. Den yttre ringen är det bästa målet för all trafik, men den inre ringen är nödvändig för att hantera trafik spill från den yttre ringen. Vad gäller VIP (Virtual Internet Protocol-adresser) tilldelas varje klient dels värd eller domän som betjänas av front dörren en primär VIP, som meddelas av miljöer i både den inre och yttre ringen, samt en reserv-VIP, som endast annonseras av miljöer i den inre ringen. 

</br>Den här övergripande strategin garanterar att förfrågningar från dina slutanvändare alltid når den närmaste främre dörren och att även om den önskade front dörr miljön inte är felfri flyttas trafiken automatiskt till nästa närmaste miljö.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Ansluta till en front dörrs miljö (delad TCP)

[Delad TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) är en teknik för att minska fördröjnings-och TCP-problem genom att bryta en anslutning som skulle innebära en hög fördröjnings tid i mindre delar.  Genom att placera de främre dörr miljöerna närmare slutanvändare och avsluta TCP-anslutningar i den främre dörren, är en TCP-anslutning med en stor tur och svars tid i program Server delen uppdelad i två TCP-anslutningar. Den korta anslutningen mellan slutanvändaren och den främre dörren innebär att anslutningen upprättas över tre korta turer i stället för tre långa turer, vilket sparar svars tiden.  Den långa anslutningen mellan frontend-miljön och Server delen kan företableras och återanvändas över flera slut användar samtal, och sedan spara TCP-anslutningssträngen igen.  Resultatet multipliceras när du etablerar en SSL/TLS-anslutning (Transport Layer Security) eftersom det finns fler tur och ingångar för att skydda anslutningen.

## <a name="processing-request-to-match-a-routing-rule"></a>Bearbetar begäran för att matcha en regel för routning
När du har upprättat en anslutning och gjort en TLS-handskakning, är det första steget när en begäran skickas till en front dörr miljö. Den här matchningen bestäms i princip för alla konfigurationer i front dörren, vilken specifika routningsregler som ska matcha begäran till. Läs om hur front dörr [matchar väg matchning](front-door-route-matching.md) för att lära dig mer.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identifiera tillgängliga Server delar i backend-poolen för Routningsprincipen
När front dörren har en matchning för en routningstabell som baseras på inkommande begäran och om det inte finns någon cachelagring, är nästa steg att hämta hälso avsöknings statusen för den backend-pool som är associerad med den matchade vägen. Läs om hur front dörren övervakar Server delens hälso tillstånd med hjälp av [hälso avsökningar](front-door-health-probes.md) för mer information.

## <a name="forwarding-the-request-to-your-application-backend"></a>Vidarebefordra begäran till din program Server del
Slutligen, förutsatt att ingen cachelagring har kon figurer ATS, vidarebefordras användar förfrågan till Server delen "bästa" baserat på konfigurationen för [routningsmetod](front-door-routing-methods.md) .

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
