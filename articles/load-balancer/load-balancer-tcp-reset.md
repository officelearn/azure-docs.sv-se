---
title: Load Balancer TCP-återställning vid inaktivitet i Azure
titleSuffix: Azure Load Balancer
description: I den här artikeln får du lära dig mer om Azure Load Balancer med dubbelriktade TCP-paket för inaktivitet vid inaktivitet.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80294410"
---
# <a name="load-balancer-with-tcp-reset-on-idle"></a>Load Balancer med TCP-återställning vid inaktivitet

Du kan använda [standard Load Balancer](load-balancer-standard-overview.md) för att skapa ett mer förutsägbart program beteende för dina scenarier genom att aktivera TCP-återställning vid inaktivitet för en viss regel. Load Balancerens standard beteende är att tyst släppa flöden när tids gränsen för inaktivitet för ett flöde uppnås.  Om du aktiverar den här funktionen kommer Load Balancer att skicka dubbelriktade TCP-återställningar (TCP-paket) vid inaktivitet.  Detta kommer att informera dina program slut punkter om att anslutningen har nått sin tids gräns och inte längre kan användas.  Slut punkter kan omedelbart upprätta en ny anslutning om det behövs.

![Load Balancer TCP-återställning](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
Du ändrar det här standard beteendet och aktiverar sändning av TCP-återställning vid inaktivitet på inkommande NAT-regler, belastnings Utjämnings regler och [utgående regler](https://aka.ms/lboutboundrules).  När den aktive ras per regel kommer Load Balancer att skicka dubbelriktad TCP-återställning (TCP-paket) till både klient-och Server slut punkter vid tidpunkten för tids gränsen för inaktivitet för alla matchande flöden.

Slut punkter som tar emot TCP-och-paket stänger motsvarande socket direkt. Detta ger en omedelbar avisering till slut punkterna som lanseringen av anslutningen har inträffat och eventuell framtida kommunikation på samma TCP-anslutning.  Program kan rensa anslutningar när socketen stängs och återupprätta anslutningar vid behov utan att vänta på att TCP-anslutningen ska ta slut på timeout.

I många fall kan detta minska behovet av att skicka TCP (eller program lagret) keepalive för att uppdatera tids gränsen för inaktivitet i ett flöde. 

Om dina inaktiva varaktigheter överstiger de som tillåts av konfigurationen eller om programmet visar att TCP-återställningar har Aktiver ATS, kan du fortfarande behöva använda TCP keepalive (eller program lagrets keepalive) för att övervaka Live-anslutningarna för TCP-anslutningarna.  Dessutom kan keepalive-objekt även vara användbara för när anslutningen är via proxy någonstans i sökvägen, särskilt program lagrets keepalive.  

Undersök noggrant hela slut punkt till slut punkt för att avgöra om du har nytta av att aktivera TCP-återställningar, justera tids gränsen för inaktivitet och om ytterligare steg kan krävas för att säkerställa att det önskade programmet fungerar.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Aktiverar timeout för TCP-återställning vid inaktivitet

Med hjälp av API-version 2018-07-01 kan du aktivera sändning av dubbelriktad TCP-återställning vid inaktivitet per regel:

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

## <a name="region-availability"></a><a name="regions"></a>Tillgänglighet för regioner

Tillgängligt i alla regioner.

## <a name="limitations"></a>Begränsningar

- TCP-endast skickade under TCP-anslutning i upprättat läge.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [standard Load Balancer](load-balancer-standard-overview.md).
- Läs mer om [utgående regler](load-balancer-outbound-rules-overview.md).
- [Konfigurera TCP-tidsgräns vid inaktivitet](load-balancer-tcp-idle-timeout.md)
