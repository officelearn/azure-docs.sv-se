---
title: Vad är Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Översikt över funktioner, arkitektur och implementering av Azure Load Balancer-funktioner. Lär dig hur Load Balancer fungerar och hur du använder det i molnet.
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
ms.openlocfilehash: 1bc18788019c3ec97e06e3b01e823a0ba53541b8
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734764"
---
# <a name="what-is-azure-load-balancer"></a>Vad är Azure Load Balancer?

*Belastnings utjämning* syftar på att fördela belastningen jämnt (inkommande nätverks trafik) över en grupp server dels resurser eller servrar. 

Azure Load Balancer arbetar i lager fyra av OSI-modellen (Open Systems Interconnection). Det är den enda kontakt punkten för-klienter. Load Balancer distribuerar inkommande flöden som anländer till belastningsutjämnarens klient del till instanser av backend-poolen. Dessa flöden sker enligt konfigurerade regler för belastnings utjämning och hälso avsökningar. Instanserna för backend-poolen kan vara Azure-Virtual Machines eller instanser i en skalnings uppsättning för virtuella datorer.

En **[offentlig belastningsutjämnare](./components.md#frontend-ip-configurations)** kan tillhandahålla utgående anslutningar för virtuella datorer (VM) i det virtuella nätverket. Dessa anslutningar utförs genom att översätta sina privata IP-adresser till offentliga IP-adresser. Offentliga belastnings utjämning används för att belastningsutjämna Internet trafik till dina virtuella datorer.

En **[intern (eller privat) belastningsutjämnare](./components.md#frontend-ip-configurations)** används där privata IP-adresser bara behövs på klient delen. Interna belastnings utjämning används för att belastningsutjämna trafik i ett virtuellt nätverk. En klient del för belastningsutjämnare kan nås från ett lokalt nätverk i ett hybrid scenario.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="512" title="Azure Load Balancer">
</p>

*Bild: balansera program på flera nivåer med både offentliga och interna Load Balancer*

Mer information om de enskilda komponenterna för belastnings utjämning finns i [Azure Load Balancer-komponenter](./components.md).

## <a name="why-use-azure-load-balancer"></a>Varför ska jag använda Azure Load Balancer?
Med Standard Load Balancer kan du skala dina program och skapa tjänster med hög tillgänglighet. Belastnings utjämning stöder både inkommande och utgående scenarier. Load Balancer ger låg latens och högt data flöde, och skalar upp till miljon tals flöden för alla TCP-och UDP-program.

Viktiga scenarier som du kan utföra med Standard Load Balancer inkluderar:

- Belastningsutjämna **[intern](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** och **[extern](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** trafik till virtuella Azure-datorer.

- Öka tillgängligheten genom att distribuera resurser **[inom](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** och **[mellan](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** zoner.

- Konfigurera **[utgående anslutning](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** för virtuella Azure-datorer.

- Använd **[hälso avsökningar](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** för att övervaka belastningsutjämnade resurser.

- Använd **[port vidarebefordring](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** för att få åtkomst till virtuella datorer i ett virtuellt nätverk via offentlig IP-adress och port.

- Aktivera stöd för **[belastnings utjämning](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** av **[IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)**.

- Standard Load Balancer ger flerdimensionella mått via [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).  Dessa mått kan filtreras, grupperas och delas upp för en specifik dimension.  De tillhandahåller aktuella och historiska insikter om prestanda och hälsa för din tjänst.  Resource Health stöds också. Granska **[standard Load Balancer Diagnostics](load-balancer-standard-diagnostics.md)** för mer information.

- Belastnings Utjämnings tjänster på **[flera portar, flera IP-adresser eller både](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)** och.

- Flytta **[interna](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** och **[externa](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** belastnings Utjämnings resurser i Azure-regioner.

- Belastningsutjämna TCP-och UDP-flöde på alla portar samtidigt med **[ha-portar](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)**.

### <a name="secure-by-default"></a><a name="securebydefault"></a>Säker som standard

Standard Load Balancer bygger på den nolla förtroendet för nätverks säkerhets modellen i dess kärna. Standard Load Balancer skyddas som standard och ingår i ditt virtuella nätverk. Det virtuella nätverket är ett privat och isolerat nätverk.  Det innebär att standard belastnings utjämning och offentliga standard-IP-adresser stängs till inkommande flöden om de inte öppnas av nätverks säkerhets grupper. NSG: er används för att uttryckligen tillåta tillåten trafik.  Om du inte har en NSG på ett undernät eller ett nätverkskort för den virtuella dator resursen, tillåts inte trafik att komma åt den här resursen. Mer information om NSG: er och hur du tillämpar dem för ditt scenario finns i [nätverks säkerhets grupper](../virtual-network/security-overview.md).
Basic Load Balancer är öppet för Internet som standard.

## <a name="pricing-and-sla"></a>Priser och service nivå avtal

Standard Load Balancer pris information finns i [Load Balancer prissättning](https://azure.microsoft.com/pricing/details/load-balancer/).
Basic Load Balancer tillhandahålls kostnadsfritt.
Se [SLA för Load Balancer](https://aka.ms/lbsla). Basic Load Balancer har inget service avtal.

## <a name="next-steps"></a>Nästa steg
Mer information finns i [uppgradera en Basic-Load Balancer](upgrade-basic-standard.md) för att uppgradera Basic Load Balancer till standard Load Balancer.

Se [skapa en offentlig standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) för att komma igång med att använda en Load Balancer.

Mer information om Azure Load Balancer begränsningar och komponenter finns [Azure Load Balancer komponenter](./components.md) och [Azure Load Balancer koncept](./concepts.md)

En jämförelse mellan alternativen för belastnings utjämning i Azure finns i [Översikt över alternativ för belastnings utjämning i Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).
