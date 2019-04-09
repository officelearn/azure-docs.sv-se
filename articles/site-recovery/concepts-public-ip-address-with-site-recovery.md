---
title: Använda offentliga IP-adresser efter redundansväxling med Azure Site Recovery | Microsoft Docs
description: Beskriver hur du ställer in den offentliga IP-adresser med Azure Site Recovery och Azure Traffic Manager för katastrofåterställning och migrering
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 1f20818f0b899eede9fff05d71e98c8bffb94b0a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280136"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Konfigurera offentliga IP-adresser efter redundansväxling

Offentliga IP-adresser tillåter att Internet-resurser kommunicerar inkommande till Azure-resurser. Offentliga IP-adresser gör det också möjligt för Azure-resurser att kommunicera utgående till Internet och offentliga Azure-tjänster med en IP-adress som tilldelats resursen.
- Inkommande kommunikation från Internet till resurs, till exempel Azure-datorer (VM), Azure Application Gateway, belastningsutjämnare för Azure, Azure VPN gateway och andra. Du kan fortfarande kommunicera med vissa resurser, till exempel virtuella datorer från Internet, om en virtuell dator inte har en offentlig IP-adress som tilldelats, så länge som den virtuella datorn är en del av en load balancer backend-poolen och belastningsutjämnaren har tilldelats en offentlig IP-adress.
- Utgående anslutning till Internet med en förutsägbar IP-adress. Exempelvis kan en virtuell dator kommunicera utgående till Internet utan en offentlig IP-adress tilldelas till den, men dess adress är nätverksadress översättas av Azure till en oförutsägbara offentlig adress som standard. Tilldela en offentlig IP-adress till en resurs kan du vet vilken IP-adress används för den utgående anslutningen. Om förutsägbara, kan adressen ändras beroende på vilken tilldelningsmetod som valts. Mer information finns i [skapa en offentlig IP-adress](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address). Läs mer om utgående anslutningar från Azure-resurser i [förstå utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

I Azure Resource Manager, en offentlig IP-adress är en resurs som har sina egna egenskaper. Följande är några av resurserna du kan associera med en offentlig IP-adressresurs:

* Nätverksgränssnitt för virtuella datorer
* Internetuppkopplade lastbalanserare
* VPN-gateways
* Programgateways

Den här artikeln beskrivs hur du kan använda offentliga IP-adresser med Site Recovery.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Offentliga IP-adresstilldelning med hjälp av Återställningsplanen

Offentliga IP-adressen för produktionsprogram **kan inte hållas kvar på redundans**. Arbetsbelastningar som visas som en del av redundans måste tilldelas en resurs för Azure offentlig IP-adress som är tillgängliga i målregionen. Det här steget kan göras antingen manuellt eller automatiserade med återställningsplaner. En återställningsplan samlar datorer i grupper för återställning. Det hjälper dig att definiera en systematisk återställningsprocessen. Du kan använda en återställningsplan för att införa ordning och automatisera de åtgärder som behövs i varje steg med hjälp av Azure Automation-runbooks för redundans till Azure eller skript.

Installationen är följande:
- Skapa en [återställningsplanen](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) och gruppera dina arbetsbelastningar vid behov i planen.
- Anpassa schemat genom att lägga till ett steg om du vill koppla en offentlig IP-adress med hjälp av [Azure Automation-runbooks](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) skript till den redundansväxlade virtuella datorn.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Offentlig slutpunkt växlar med DNS-nivå Routning

Azure Traffic Manager kan DNS-nivå routning mellan slutpunkter och kan hjälpa dig med [att minska dina mål för återställningstid](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) för katastrofåterställning. 

Läs mer om redundansscenarier med Traffic Manager:
1. [Lokalt till Azure-redundans](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) med Traffic Manager 
2. [Azure-datorer redundans](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) med Traffic Manager 

Installationen är följande:
- Skapa en [Traffic Manager-profil](../traffic-manager/traffic-manager-create-profile.md).
- Med den **prioritet** metoden, skapar du två slutpunkter – **primära** för källa och **redundans** för Azure. **Primär** är tilldelad prioritet 1 och **redundans** är tilldelad prioritet 2.
- Den **primära** kan vara [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) eller [externa](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) beroende på om din källmiljö är innanför eller utanför Azure.
- Den **redundans** slutpunkten har skapats som en **Azure** slutpunkt. Använd en **statisk offentlig IP-adress** som det här är extern slutpunkt mot för Traffic Manager i händelsen haveriberedskap.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Traffic Manager med Azure Site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- Läs mer om Traffic Manager [routningsmetoder](../traffic-manager/traffic-manager-routing-methods.md).
- Läs mer om [återställningsplaner](site-recovery-create-recovery-plans.md) vill automatisera redundans i programmet.
