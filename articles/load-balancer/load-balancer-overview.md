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
ms.date: 1/14/2019
ms.author: allensu
ms.openlocfilehash: dc986d40d50b93720c87ba36d265ed3044b0abc9
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045388"
---
# <a name="what-is-azure-load-balancer"></a>Vad är Azure Load Balancer?

*Belastnings utjämning* syftar på att fördela belastningen jämnt (inkommande nätverks trafik) över en grupp server dels resurser eller servrar. Azure erbjuder en [mängd olika alternativ för belastnings utjämning](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) som du kan välja bland utifrån dina behov. Det här dokumentet täcker Azure Load Balancer.

Azure Load Balancer arbetar i lager fyra av OSI-modellen (Open Systems Interconnection). Det är den enda kontakt punkten för-klienter. Load Balancer distribuerar nya inkommande flöden som anländer till belastningsutjämnarens klient del till backend-instanser. Dessa flöden sker enligt konfigurerade regler för belastnings utjämning och hälso avsökningar. Backend-poolens instanser kan vara Azure Virtual Machines eller instanser i en skalnings uppsättning för virtuella datorer.


En **[offentlig belastningsutjämnare](./concepts-limitations.md#publicloadbalancer)** kan tillhandahålla utgående anslutningar för virtuella datorer (VM) i det virtuella nätverket. Dessa anslutningar utförs genom att översätta sina privata IP-adresser till offentliga IP-adresser. Offentliga belastnings utjämning används för att belastningsutjämna Internet trafik till dina virtuella datorer.

En **[intern (eller privat) belastningsutjämnare](./concepts-limitations.md#internalloadbalancer)** används där privata IP-adresser bara behövs på klient delen. Interna belastnings utjämning används för att belastningsutjämna trafik i ett virtuellt nätverk. En klient del för belastningsutjämnare kan nås från ett lokalt nätverk i ett hybrid scenario.

<div align="center">
  <img src='./media/load-balancer-overview/IC744147.png'>
</div>

*Bild: balansera program på flera nivåer med både offentliga och interna Load Balancer*

Mer information om de enskilda komponenterna för belastnings utjämning finns i [Azure Load Balancer komponenter och begränsningar](./concepts-limitations.md)

>[!NOTE]
> Microsoft rekommenderar [standard Load Balancer](./load-balancer-standard-overview.md).
Fristående virtuella datorer, tillgänglighetsuppsättningar och VM-skalningsuppsättningar kan bara anslutas till en SKU, aldrig båda. Load Balancer och den offentliga IP-adressens SKU måste matcha när du använder dem med offentliga IP-adresser. Load Balancer och offentliga IP-SKU: er är inte föränderligt.

## <a name="why-use-azure-load-balancer"></a>Varför ska jag använda Azure Load Balancer?
Med Azure Load Balancer kan du skala dina program och skapa tjänster med hög tillgänglighet. Belastnings utjämning stöder både inkommande och utgående scenarier. Load Balancer ger låg latens och högt data flöde, och skalar upp till miljon tals flöden för alla TCP-och UDP-program.

Viktiga scenarier som du kan utföra med Azure Load Balancer inkluderar:

- Belastningsutjämna **[intern](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** och **[extern](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** trafik till virtuella Azure-datorer.

- Öka tillgängligheten genom att distribuera resurser **[inom](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** och **[mellan](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** zoner.

- Konfigurera **[utgående anslutning](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** för virtuella Azure-datorer.

- Använd **[hälso avsökningar](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** för att övervaka belastningsutjämnade resurser.

- Använd **[port vidarebefordring](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** för att få åtkomst till virtuella datorer i ett virtuellt nätverk via offentlig IP-adress och port.

- Aktivera stöd för **[belastnings utjämning](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** av **[IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)** .

- Dra nytta av **[mått och diagnostik](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)** för Azure Load Balancer med **[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)** .

- Belastnings Utjämnings tjänster på **[flera portar, flera IP-adresser eller både](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)** och.

- Flytta **[interna](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** och **[externa](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** belastnings Utjämnings resurser i Azure-regioner.

- Belastningsutjämna TCP-och UDP-flöde på alla portar samtidigt med **[ha-portar](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)** .

## <a name="pricing"></a>Prissättning

Standard Load Balancer-användning debiteras.

* Antal konfigurerade regler för belastnings utjämning och utgående trafik. Inkommande NAT-regler räknas inte i det totala antalet regler.
* Mängden data som bearbetas inkommande och utgående oberoende av regler.

Standard Load Balancer pris information finns i [Load Balancer prissättning](https://azure.microsoft.com/pricing/details/load-balancer/).

Basic Load Balancer tillhandahålls kostnadsfritt.

## <a name="sla"></a>SLA

Information om Standard Load Balancer SLA finns i [SLA för Load Balancer](https://aka.ms/lbsla).

## <a name="next-steps"></a>Nästa steg

Se [skapa en offentlig standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) för att komma igång med att använda en Load Balancer.

Mer information om Azure Load Balancer begränsningar och komponenter finns [Azure Load Balancer begrepp och begränsningar](./concepts-limitations.md)
