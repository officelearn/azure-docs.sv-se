---
title: Azure Load Balancer typer
description: Översikt över Azure Load Balancer typer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2020
ms.author: allensu
ms.openlocfilehash: 31e2bf19967bb8870ee6ab75687bb3fcc37373f7
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629975"
---
# <a name="azure-load-balancer-types"></a>Azure Load Balancer typer

Azure Load Balancer har två typer och två SKU: er.

## <a name="public-load-balancer"></a><a name = "publicloadbalancer"></a>Offentlig belastningsutjämnare

En offentlig belastningsutjämnare mappar den offentliga IP-adressen och porten för inkommande trafik till den privata IP-adressen och porten för den virtuella datorn. Belastnings utjämning mappar trafik på det andra sättet för svars trafiken från den virtuella datorn. Du kan distribuera vissa typer av trafik över flera virtuella datorer eller tjänster genom att använda regler för belastnings utjämning. Du kan till exempel sprida belastningen av trafik för webbegäranden på flera webbservrar.

>[!NOTE]
>Du kan endast implementera en offentlig belastningsutjämnare och en intern belastningsutjämnare per tillgänglighets uppsättning.

Följande bild visar en belastningsutjämnad slut punkt för webb trafik som delas mellan tre virtuella datorer för den offentliga och TCP-port 80. Dessa tre virtuella datorer finns i en belastningsutjämnad uppsättning.

![Exempel på offentlig belastningsutjämnare](./media/load-balancer-overview/load-balancer.png)

*Bild: utjämna webb trafik med hjälp av en offentlig belastningsutjämnare*

Internet klienter skickar webb sidor begär anden till den offentliga IP-adressen för en webbapp på TCP-port 80. Azure Load Balancer distribuerar begär Anden mellan de tre virtuella datorerna i den belastningsutjämnade uppsättningen. Mer information om algoritmer för belastnings utjämning finns i [belastnings Utjämnings begrepp](concepts.md).

Azure Load Balancer distribuerar nätverks trafiken jämnt mellan flera virtuella dator instanser som standard. Du kan även konfigurera sessionstillhörighet. Mer information finns i [Konfigurera distributions läge för Azure Load Balancer](load-balancer-distribution-mode.md).

## <a name="internal-load-balancer"></a><a name = "internalloadbalancer"></a>Intern belastningsutjämnare

En intern belastningsutjämnare distribuerar trafik till resurser som finns i ett virtuellt nätverk. Azure begränsar åtkomsten till klient delens IP-adresser för ett virtuellt nätverk som är belastningsutjämnad. 

IP-adresser och virtuella nätverk på klient sidan exponeras aldrig direkt till en Internet-slutpunkt. Interna verksamhetsspecifika appar körs i Azure och nås i Azure eller via lokala resurser.

En intern belastningsutjämnare aktiverar följande typer av belastnings utjämning:

* **I ett virtuellt nätverk**: belastnings utjämning från virtuella datorer i det virtuella nätverket till en uppsättning virtuella datorer som finns i samma virtuella nätverk.
* **För ett virtuellt nätverk mellan olika platser**: belastnings utjämning från lokala datorer till en uppsättning virtuella datorer som finns i samma virtuella nätverk.
* **För program på flera nivåer**: belastnings utjämning för Internet riktade program på flera nivåer där backend-nivåerna inte är Internet-riktade. Backend-nivåerna kräver trafik belastnings utjämning från den Internetbaserade nivån. Se nästa figur.
* **För verksamhetsspecifika appar**: Lastbalansering för verksamhetsspecifika appar som finns i Azure utan ytterligare maskin- eller programvara för lastbalanserare. Det här scenariot omfattar lokala servrar som finns i den uppsättning datorer vars trafik är belastningsutjämnad.

![Exempel på intern lastbalanserare](./media/load-balancer-overview/load-balancer.png)

*Bild: utjämna program på flera nivåer med både offentlig och intern belastningsutjämnare*

## <a name="load-balancer-sku-comparison"></a><a name="skus"></a> Load Balancer SKU:er – jämförelse

Load Balancer stöder både Basic-och standard-SKU: er. Dessa SKU: er skiljer sig åt i scenario skala, funktioner och priser. Alla scenarier som är möjliga med Basic Load Balancer kan skapas med standard Load Balancer.

Du kan jämföra och se skillnaderna i tabellen nedan. Mer information finns i [Översikt över Azure standard Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> Microsoft rekommenderar standard Load Balancer.
Fristående virtuella datorer, tillgänglighetsuppsättningar och VM-skalningsuppsättningar kan bara anslutas till en SKU, aldrig båda. Belastningsutjämnaren och den offentliga IP-adressens SKU måste matcha när du använder dem med offentliga IP-adresser. Belastnings utjämning och offentliga IP-SKU: er är inte föränderligt.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Mer information finns i [gränser för belastnings utjämning](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Information om Standard Load Balancer finns i [översikt](load-balancer-standard-overview.md), [prissättning](https://aka.ms/lbpricing) och [serviceantal](https://aka.ms/lbsla).

## <a name="next-steps"></a>Nästa steg

- Se [skapa en offentlig standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) för att komma igång med att använda en Load Balancer.
- Lär dig mer om att använda [standard Load Balancer och Tillgänglighetszoner](load-balancer-standard-availability-zones.md).
- Läs mer om [hälso avsökningar](load-balancer-custom-probe-overview.md).
- Lär dig mer om hur du använder [Load Balancer för utgående anslutningar](load-balancer-outbound-connections.md).
- Lär dig mer om att [standard Load Balancer med belastnings Utjämnings regler för belastnings utjämning](load-balancer-ha-ports-overview.md).
- Läs mer om [nätverks säkerhets grupper](../virtual-network/security-overview.md).