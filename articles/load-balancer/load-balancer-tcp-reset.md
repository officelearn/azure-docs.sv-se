---
title: Läsa in belastningsutjämnare TCP återställs med inaktiva | Microsoft Docs
description: Belastningsutjämnare med dubbelriktad TCP RSTA paket på timeout för inaktivitet
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2018
ms.author: kumud
ms.openlocfilehash: 3cc4db387c33388d5692fa25ac26f2b6399cd185
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879850"
---
# <a name="load-balancer-with-tcp-reset-on-idle-public-preview"></a>Belastningsutjämnare med TCP-återställning på inaktiv (offentlig förhandsversion)

Du kan använda [Standardbelastningsutjämnare](load-balancer-standard-overview.md) skapar du en mer förutsägbar programmets beteende för dina scenarier med dubbelriktad TCP återställer (TCP RSTA paketet) för varje konfigurerbara timeout för inaktivitet.  Standardbeteendet för Load Balancer är att släppa tyst flöden när tidsgränsen för inaktivitet i ett flöde har uppnåtts.

![Load Balancer TCP-återställning](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)

>[!NOTE] 
>Belastningsutjämnare med TCP nollställs timeout för inaktivitet funktioner är tillgängliga just nu som offentlig förhandsversion och är tillgänglig i en begränsad uppsättning [regioner](#regions). Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Du kan ändra det här standardbeteendet och aktivera skicka TCP återställer på timeout för inaktivitet på inkommande NAT-regler, belastningsutjämningsregler, och [utgående regler](https://aka.ms/lboutboundrules).  När aktiverat per regel belastningsutjämnaren skickar dubbelriktad TCP-återställning (TCP RSTA paket) till både klienten och servern slutpunkter vid tidpunkten för timeout för inaktivitet för alla matchande flöden.

Slutpunkter tar emot TCP RSTA paket Stäng motsvarande socket omedelbart. Detta ger en omedelbar avisering till de slutpunkter som versionen av anslutningen har inträffat och eventuella framtida kommunikation på samma TCP-anslutningen misslyckas.  Program kan rensa anslutningar när socket stängs och återupprätta anslutningarna efter behov utan att behöva vänta TCP-anslutningen så småningom uppnår en tidsgräns.

Detta kan minska keepalive-överföringar för behovet av att skicka TCP (eller application layer) för att uppdatera tidsgränsen för inaktivitet i ett flöde för många scenarier. 

Om din inaktiva varaktigheter överskrider de tillåts av konfigurationen eller ditt program visar ett oönskat beteende med TCP återställer aktiverad, kan du fortfarande behöva använda TCP keepalive-överföringar (eller application layer keepalive-överföringar) för att övervaka liveness för TCP-anslutningar.  Keepalive-överföringar kan dessutom också är användbara för när anslutningen är via proxy någonstans i sökvägen, särskilt program layer keepalive-överföringar.  

Granska noggrant hela slutpunkt till slutpunkt-scenariot för att bestämma om du dra nytta av att aktivera TCP återställer justera tidsgränsen för inaktivitet, och om ytterligare steg kan krävas för att kontrollera att önskad programmets beteende.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Att aktivera TCP-återställning på timeout för inaktivitet

Med API-versionen 2018-07-01 kan aktivera du utskick av dubbelriktad TCP återställer på timeout för inaktivitet på basis av per regel:

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

## <a name="regions"></a> Regiontillgänglighet

Den här parametern är giltiga för närvarande i följande regioner.  Parametern har ingen effekt i regioner som inte visas här.

| Region |
|---|
| Sydostasien |
| Södra Brasilien |
| Centrala Kanada |
| Västeuropa |
| Centrala Indien |
| Västra Indien |
| Västra Japan |
| Sydkorea, centrala |
| Sydkorea, södra |
| Storbritannien, norra |
| Storbritannien, södra 2 |
| USA, Östra |
| USA, östra 2 |
| USA, Norra |
| USA, Västra |

Den här tabellen kommer att uppdateras när förhandsversionen utökas till andra regioner.  

## <a name="limitations"></a>Begränsningar

- Begränsad [regiontillgänglighet](#regions).
- Portalen kan inte användas för att konfigurera eller visa TCP-återställning.  Använd mallar, REST API, Az CLI 2.0 eller PowerShell i stället.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Standardbelastningsutjämnare](load-balancer-standard-overview.md).
- Lär dig mer om [utgående regler](load-balancer-outbound-rules-overview.md).
