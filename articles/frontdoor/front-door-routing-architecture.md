---
title: Azure Ytterdörr - routning arkitektur | Microsoft-dokument
description: Den här artikeln hjälper dig att förstå den globala synaspekten av Ytterdörrens arkitektur.
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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879300"
---
# <a name="routing-architecture-overview"></a>Översikt över routningsarkitektur

Azure Front Door när den tar emot dina klientbegäranden då den antingen svarar dem (om cachelagring är aktiverad) eller vidarebefordrar dem till rätt program backend (som en omvänd proxy).

</br>Det finns möjligheter att optimera trafiken vid routning till Azure Front Door samt vid routning till backends.

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>Välja ytterdörrsmiljön för trafikroutning (Anycast)

Routning till Azure Front Door-miljöer utnyttjar [Anycast](https://en.wikipedia.org/wiki/Anycast) för både DNS-trafik (Domain Name System) och HTTP-trafik (Hypertext Transfer Protocol), så användartrafik kommer att gå till den närmaste miljön när det gäller nätverkstopologi (minst hopp). Den här arkitekturen erbjuder vanligtvis bättre tur-och-retur-tider för slutanvändare (maximera fördelarna med Split TCP). Ytterdörren organiserar sina miljöer i primära och reserv "ringar".  Den yttre ringen har miljöer som är närmare användarna, som erbjuder lägre fördröjningar.  Den inre ringen har miljöer som kan hantera redundans för den yttre ringen miljön om ett problem inträffar. Den yttre ringen är det föredragna målet för all trafik, men den inre ringen är nödvändig för att hantera trafikspill från den yttre ringen. När det gäller VIP (Virtual Internet Protocol-adresser), tilldelas varje frontend värd, eller domän som betjänas av Ytterdörren en primär VIP, som aviserats av miljöer i både den inre och yttre ringen, samt en reserv-VIP, som endast tillkännages av miljöer i den inre ringen. 

</br>Den här övergripande strategin säkerställer att begäranden från dina slutanvändare alltid når närmaste frontdörrsmiljö och att även om den önskade ytterdörren-miljön är ohälsosam så flyttas trafiken automatiskt till nästa närmaste miljö.

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Ansluta till ytterdörrens miljö (Split TCP)

[Split TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) är en teknik för att minska latenser och TCP problem genom att bryta en anslutning som skulle medföra en hög rundresa tid i mindre bitar.  Genom att placera frontdörrsmiljöerna närmare slutanvändarna och avsluta TCP-anslutningar inuti ytterdörrens miljö, delas en TCP-anslutning med en stor rundresa (RTT) till ansökan backend upp i två TCP-anslutningar. Den korta anslutningen mellan slutanvändaren och ytterdörren miljön innebär att anslutningen blir etablerad över tre korta tur och returresor i stället för tre långa rundresor, vilket sparar latens.  Den långa anslutningen mellan ytterdörren och backend kan förkonetables och återanvändas över flera slutanvändare samtal, återigen spara TCP anslutningstiden.  Effekten multipliceras när du upprättar en SSL/TLS-anslutning (Transport Layer Security), eftersom det finns fler tur- och returresor för att säkra anslutningen.

## <a name="processing-request-to-match-a-routing-rule"></a>Bearbeta begäran om att matcha en routningsregel
När du har upprättat en anslutning och gjort ett TLS-handslag, när en begäran landar på en ytterdörrsmiljö, är matchning av en routningsregel det första steget. Den här matchningen avgör i princip från alla konfigurationer i Ytterdörren, vilken särskild routningsregel som ska matcha begäran till. Läs om hur Ytterdörren gör [ruttmatchning](front-door-route-matching.md) för att lära dig mer.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identifiera tillgängliga backends i backend-poolen för routningsregeln
När Ytterdörren har en matchning för en routningsregel baserat på den inkommande begäran och om det inte finns någon cachelagring, är nästa steg att dra hälsoavsökningsstatusen för serverdelspoolen som är associerad med den matchade vägen. Läs om hur Front Door övervakar backend hälsa med hjälp av [Hälsosonder](front-door-health-probes.md) för att lära sig mer.

## <a name="forwarding-the-request-to-your-application-backend"></a>Vidarebefordra begäran till din ansökan backend
Slutligen, förutsatt att det inte finns någon cachelagring konfigurerad, är användaren begäran vidarebefordras till den "bästa" serverdelen baserat på din [Front Door routing metod](front-door-routing-methods.md) konfiguration.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
