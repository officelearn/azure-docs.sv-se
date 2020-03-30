---
title: Tilldela offentliga IP-adresser efter redundans med Azure Site Recovery
description: Beskriver hur du konfigurerar offentliga IP-adresser med Azure Site Recovery och Azure Traffic Manager för haveriberedskap och migrering
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: b1f3ffa6fc90fc0cab0217d1b71907342f2dbd0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281956"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Konfigurera offentliga IP-adresser efter redundans

Offentliga IP-adresser tillåter att Internet-resurser kommunicerar inkommande till Azure-resurser. Offentliga IP-adresser gör det också möjligt för Azure-resurser att kommunicera utgående till Internet och offentliga Azure-tjänster med en IP-adress som tilldelats resursen.
- Inkommande kommunikation från Internet till resursen, till exempel Virtuella Azure-datorer (VM), Azure Application Gateways, Azure Load Balancers, Azure VPN Gateways och andra. Du kan fortfarande kommunicera med vissa resurser, till exempel virtuella datorer, från Internet, om en virtuell dator inte har tilldelats en offentlig IP-adress, så länge den virtuella datorn ingår i en sluten belastningsutjämnad pool och belastningsutjämnaren tilldelas en offentlig IP-adress.
- Utgående anslutning till Internet med en förutsägbar IP-adress. En virtuell dator kan till exempel kommunicera utgående till Internet utan en offentlig IP-adress som tilldelats den, men dess adress är nätverksadress som översätts av Azure till en oförutsägbar offentlig adress, som standard. Genom att tilldela en offentlig IP-adress till en resurs kan du veta vilken IP-adress som används för den utgående anslutningen. Även om den är förutsägbar kan adressen ändras, beroende på vilken tilldelningsmetod som valts. Mer information finns i [Skapa en offentlig IP-adress](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address). Mer information om utgående anslutningar från Azure-resurser finns i [Förstå utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

I Azure Resource Manager är en offentlig IP-adress en resurs som har sina egna egenskaper. Följande är några av resurserna du kan associera med en offentlig IP-adressresurs:

* Nätverksgränssnitt för virtuella datorer
* Internetuppkopplade lastbalanserare
* VPN-gateways
* Programgateways

I den här artikeln beskrivs hur du kan använda offentliga IP-adresser med Site Recovery.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Tilldelning av offentlig IP-adress med hjälp av återställningsplan

Den offentliga IP-adressen för produktionsprogrammet **kan inte behållas vid redundans.** Arbetsbelastningar som tas upp som en del av redundansprocessen måste tilldelas en Azure Public IP-resurs som är tillgänglig i målregionen. Det här steget kan göras antingen manuellt eller automatiseras med återställningsplaner. En återställningsplan samlar maskiner i återställningsgrupper. Det hjälper dig att definiera en systematisk återställningsprocess. Du kan använda en återställningsplan för att införa ordning och automatisera de åtgärder som behövs i varje steg med hjälp av Azure Automation-runbooks för redundans till Azure eller skript.

Inställningen är följande:
- Skapa en [återställningsplan](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) och gruppera dina arbetsbelastningar efter behov i planen.
- Anpassa planen genom att lägga till ett steg för att bifoga en offentlig IP-adress med [Azure Automation runbooks](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) skript till den misslyckade över den virtuella datorn.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Offentlig slutpunktsväxling med ROUTning på DNS-nivå

Azure Traffic Manager möjliggör ROUTning på DNS-nivå mellan slutpunkter och kan hjälpa till med [att köra ner dina RTOs](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) för ett DR-scenario. 

Läs mer om redundansscenarier med Traffic Manager:
1. [Lokal till Azure-redundans](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) med Traffic Manager 
2. [Azure till Azure redundans](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) med Traffic Manager 

Inställningen är följande:
- Skapa en [Traffic Manager-profil](../traffic-manager/traffic-manager-create-profile.md).
- Använd routningsmetoden **Prioritet,** skapa två slutpunkter – **Primär** för källa och **Redundans** för Azure. **Primär** tilldelas Prioritet 1 och **Redundans** tilldelas Prioritet 2.
- Den **primära** slutpunkten kan vara [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) eller [Extern](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) beroende på om källmiljön finns i eller utanför Azure.
- **Redundansslutpunkten** skapas som en **Azure-slutpunkt.** Använd en **statisk offentlig IP-adress** eftersom den här kommer att vara extern motslutpunkt för Traffic Manager i katastrofhändelsen.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Traffic Manager med Azure Site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- Läs mer om [Routningsmetoder för](../traffic-manager/traffic-manager-routing-methods.md)Trafikhanteraren .
- Läs mer om [återställningsplaner](site-recovery-create-recovery-plans.md) för att automatisera programundans.
