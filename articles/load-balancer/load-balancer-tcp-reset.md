---
title: Belastningsutjämnare TCP-återställning på inaktiv i Azure
titleSuffix: Azure Load Balancer
description: Med den här artikeln kan du läsa mer om Azure Load Balancer med dubbelriktade TCP RST-paket på inaktiv tidsgränsen.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2019
ms.author: allensu
ms.openlocfilehash: d3d836ddea8d07a25ad09e6f19d9f17a680decd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294410"
---
# <a name="load-balancer-with-tcp-reset-on-idle"></a>Belastningsutjämnare med TCP-återställning på inaktiv

Du kan använda [Standard belastningsutjämning](load-balancer-standard-overview.md) för att skapa ett mer förutsägbart programbeteende för dina scenarier genom att aktivera Återställning av TCP på inaktiv för en viss regel. Belastningsutjämnarens standardbeteende är att tyst släppa flöden när tidsgränsen för inaktiv tid för ett flöde nås.  Om du aktiverar den här funktionen får belastningsutjämnaren att skicka dubbelriktade TCP-återställningar (TCP RST-paket) på inaktiv tidsgränsen.  Detta informerar programslutpunkterna om att anslutningen har nått tidsutgång och inte längre kan kan tas ut.  Slutpunkter kan omedelbart upprätta en ny anslutning om det behövs.

![Återställning av belastningsutjämnare TCP](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
Du ändrar det här standardbeteendet och aktiverar sändning av TCP-återställningar vid inaktiv tidsgränsen för inkommande NAT-regler, belastningsutjämningsregler och [utgående regler](https://aka.ms/lboutboundrules).  När det är aktiverat per regel skickar belastningsutjämnaren dubbelriktad TCP-återställning (TCP RST-paket) till både klient- och serverslutpunkter vid tidpunkten för inaktiv tidsgränsen för alla matchande flöden.

Slutpunkter som tar emot TCP RST-paket stänger motsvarande socket omedelbart. Detta ger ett omedelbart meddelande till slutpunkterna att frisläppandet av anslutningen har skett och all framtida kommunikation på samma TCP-anslutning misslyckas.  Program kan rensa anslutningar när socketen stängs och återupprätta anslutningar efter behov utan att vänta på att TCP-anslutningen så småningom ska ta time out.

I många scenarier kan detta minska behovet av att skicka TCP -(eller programlager) keepalives för att uppdatera tidsgränsen för inaktiv tid för ett flöde. 

Om inaktiva varaktigheter överskrider varaktigheten för tillåtna av konfigurationen eller om ditt program visar ett oönskat beteende med TCP-återställningar aktiverade, kan du fortfarande behöva använda TCP-keepalives (eller programlager keepalives) för att övervaka liveness av TCP-anslutningar.  Vidare kan keepalives också vara användbara för när anslutningen är proxied någonstans i vägen, särskilt applikationsskiktet keepalives.  

Undersök noggrant hela scenariot från slutpunkt till för att avgöra om du kan dra nytta av att aktivera TCP-återställningar, justera tidsgränsen för inaktiv utskrift och om ytterligare steg kan krävas för att säkerställa önskat programbeteende.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Aktivera TCP-återställning vid inaktiv timeout

Med API-version 2018-07-01 kan du aktivera sändning av dubbelriktad TCP-återställningar på inaktiv tidsgränsen per regel:

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="region-availability"></a><a name="regions"></a>Regionens tillgänglighet

Finns i alla regioner.

## <a name="limitations"></a>Begränsningar

- TCP RST skickas endast under TCP-anslutning i etablerat tillstånd.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [standardbelastningsutjämningsdon](load-balancer-standard-overview.md).
- Läs mer om [utgående regler](load-balancer-outbound-rules-overview.md).
- [Konfigurera TCP RST vid inaktiv timeout](load-balancer-tcp-idle-timeout.md)
