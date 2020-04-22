---
title: Vad är Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Översikt över funktioner, arkitektur och implementering av Azure Load Balancer-funktioner. Lär dig hur belastningsutjämnaren fungerar och hur du använder den i molnet.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/14/2020
ms.author: allensu
ms.openlocfilehash: 8596b435ffa02da7daf4ef98bfe0fe7995b9270a
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768189"
---
# <a name="what-is-azure-load-balancer"></a>Vad är Azure Load Balancer?

*Belastningsutjämning* avser jämnt fördelande av belastning (inkommande nätverkstrafik) över en grupp serverdelsresurser eller servrar. 

Azure Load Balancer fungerar på lager fyra av OSI-modellen (Open Systems Interconnection). Det är den enda kontaktpunkten för kunder. Belastningsutjämnaren distribuerar inkommande flöden som kommer fram till belastningsutjämnarens klientdel till serverdelspoolinstanser. Dessa flöden är enligt konfigurerade belastningsutjämningsregler och hälsoavsökningar. Servergruppspoolinstanserna kan vara Virtuella Azure-datorer eller instanser i en skalningsuppsättning för virtuella datorer.

En **[offentlig belastningsutjämnare](./concepts-limitations.md#publicloadbalancer)** kan tillhandahålla utgående anslutningar för virtuella datorer i det virtuella nätverket. Dessa anslutningar åstadkoms genom att översätta sina privata IP-adresser till offentliga IP-adresser. Offentliga belastningsutjämnare används för att läsa in internettrafiken till dina virtuella datorer.

En **[intern (eller privat) belastningsutjämnare](./concepts-limitations.md#internalloadbalancer)** används där privata IPs endast behövs vid frontend. Interna belastningsutjämnare används för att läsa in balanstrafik i ett virtuellt nätverk. Ett frontend för belastningsutjämnare kan nås från ett lokalt nätverk i ett hybridscenario.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="512" title="Azure Load Balancer">
</p>

*Bild: Balansera program på flera nivåer med hjälp av både offentliga och interna belastningsutjämnare*

Mer information om de enskilda belastningsutjämnandekomponenterna finns i [Azure Load Balancer-komponenter och begränsningar](./concepts-limitations.md)

>[!NOTE]
> Med Azure har du tillgång till en uppsättning fullständigt hanterade belastningsutjämningslösningar för dina scenarier. Om du behöver högpresterande, låg latens, Layer-7 belastningsutjämning, se [Vad är Azure Application Gateway?](../application-gateway/overview.md) Om du letar efter global DNS-belastningsutjämning läser du [Vad är Traffic Manager?](../traffic-manager/traffic-manager-overview.md) Dina heltäckande scenarier kan dra nytta av att kombinera dessa lösningar.
>
> En jämförelse med azure-belastningsutjämningsalternativ finns i [Översikt över belastningsutjämningsalternativ i Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="why-use-azure-load-balancer"></a>Varför använda Azure Load Balancer?
Med Standard load balancer kan du skala dina program och skapa tjänster med hög tillgång. Belastningsutjämnare stöder både inkommande och utgående scenarier. Belastningsutjämnar ger låg latens och högt dataflöde och skalar upp till miljontals flöden för alla TCP- och UDP-program.

Viktiga scenarier som du kan utföra med standardbelastningsutjämning är:

- Belastningsbalans **[intern](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** och **[extern](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** trafik till virtuella Azure-datorer.

- Öka tillgängligheten genom att distribuera resurser **[inom](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** och **[mellan](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** zoner.

- Konfigurera **[utgående anslutning](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** för virtuella Azure-datorer.

- Använd **[hälsoavsökningar](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** för att övervaka belastningsbalanserade resurser.

- Använd **[portbefordring](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** för att komma åt virtuella datorer i ett virtuellt nätverk via offentlig IP-adress och port.

- Aktivera stöd för **[belastningsutjämning](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** av **[IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)**.

- Standardbelastningsutjämnaren ger flerdimensionella mått via [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).  Dessa mått kan filtreras, grupperas och delas ut för en viss dimension.  De ger aktuella och historiska insikter om prestanda och hälsa för din tjänst.  Resurshälsa stöds också. Läs **[Standard belastningsutjämnad diagnostik](load-balancer-standard-diagnostics.md)** för mer information.

- Belastningsutjämningstjänster på **[flera portar, flera IP-adresser eller båda](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)**.

- Flytta **[interna](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** och **[externa](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** belastningsutjämnareresurser över Azure-regioner.

- Belastningsutjämna TCP- och UDP-flöde på alla portar samtidigt med **[HA-portar](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)**.

### <a name="secure-by-default"></a><a name="securebydefault"></a>Säker som standard

Standardbelastningsutjämnare bygger på nätverkssäkerhetsmodellen för nollförtroende i grunden. Standardbelastningsutjämnaren är säker som standard och är en del av det virtuella nätverket. Det virtuella nätverket är ett privat och isolerat nätverk.  Det innebär att standardbelastningsutjämningsmedel och offentliga standard-IP-adresser är stängda för inkommande flöden om de inte öppnas av nätverkssäkerhetsgrupper. NSG används för att uttryckligen tillåta tillåten trafik.  Om du inte har en NSG på ett undernät eller nätverkskort för din virtuella datorresurs, är det inte tillåtet att nå den här resursen. Mer information om NSG:er och hur du använder dem för ditt scenario finns i [Nätverkssäkerhetsgrupper](../virtual-network/security-overview.md).
Grundläggande belastningsutjämnare är öppen för internet som standard.


## <a name="pricing-and-sla"></a>Prissättning och SLA

Prisinformation för standardbelastningsutjämning finns i [Belastningsutjämnadpris](https://azure.microsoft.com/pricing/details/load-balancer/).
Basic Load Balancer tillhandahålls kostnadsfritt.
Se [SLA för belastningsutjämnare](https://aka.ms/lbsla). Grundläggande belastningsutjämnare har inget serviceavtal.

## <a name="next-steps"></a>Nästa steg
Se [Uppgradera en grundläggande belastningsutjämnare](upgrade-basic-standard.md) för att uppgradera grundläggande belastningsutjämnare till standardbelastningsutjämnare.

Se [Skapa en offentlig standardbelastningsutjämningsfaktor](quickstart-load-balancer-standard-public-portal.md) för att komma igång med en belastningsutjämnare.

Mer information om begränsningar och komponenter för Azure Load Balancer finns i [Azure Load Balancer-begrepp och begränsningar](./concepts-limitations.md)
