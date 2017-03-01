---
title: "Virtuella nätverk i Azure | Microsoft Docs"
description: "Lär dig mer om virtuella nätverk i Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 75c5b8d3d8c8f389b8cee7d5d304b6e9704252fc
ms.openlocfilehash: a57805510d5e84fcdc6c4521ae9443ec72de59e1


---
# <a name="virtual-networks"></a>Virtuella nätverk
Ett Azure-virtuellt nätverk (VNet) är en representation av ditt eget nätverk i molnet.  Det är en logisk isolering av Azure-molnet dedikerad till din prenumeration. Du kan helt styra IP-adressblocken, DNS-inställningarna, säkerhetsprinciperna och routingtabellerna inom det här nätverket. Du kan dessutom ytterligare segmentera ditt VNet i undernät och köra Azure IaaS-virtuella datorer (VM:ar) och/eller [Molntjänst (PaaS rollinstanser)](../cloud-services/cloud-services-choose-me.md). Du kan dessutom ansluta det virtuella nätverket till ditt lokala nätverk med ett av [anslutningsalternativen](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site-connections) som finns i Azure. I princip kan du expandera ditt nätverk till Azure, med fullständig kontroll över IP-adressblock och de fördelar som Azure på företagsnivå erbjuder.

För att förstå VNet bättre, kolla på bilden nedan som visar ett förenklat lokalt nätverk.

![Lokalt nätverk](./media/virtual-networks-overview/figure01.png)

Ovanstående bild visar ett lokalt nätverk som är anslutet till det offentliga Internet via en router. Du kan också se en brandvägg mellan routern och en DMZ som är värd för en DNS-server och en webbserver-farm. Webbserver-farmen är belastningsutjämnad med en maskinvaru-utjämnare som exponeras mot Internet och förbrukar resurser från det interna undernätet. Det interna undernätet är åtskilt från DMZ:n av en annan brandvägg och är värd för Active Directory-domänkontrollantservrar, databasservrar och programservrar.

Samma nätverk kan finnas på Azure som visas i bilden nedan.

![Azure-virtuellt nätverk](./media/virtual-networks-overview/figure02.png)

Observera hur Azure-infrastrukturen tar på sig rollen som router och tillåter åtkomst från ditt VNet till offentliga Internet utan att det behöver konfigureras. Brandväggar kan ersättas av nätverkssäkerhetsgrupper (NSG:er) som implementeras på varje individuellt undernät. Och fysiska belastningsutjämnare ersätts av internetriktade och interna belastningsutjämnare i Azure.

> [!NOTE]
> Det finns två distributionslägen i Azure: klassisk (också känt som Service Management) och Azure Resource Manager (ARM). Klassiska VNet kan läggas till i en tillhörighetsgrupp eller skapas som ett regionalt VNet. Om du har ett VNet i en tillhörighetsgrupp, rekommenderas det att [migrera det till ett regionalt VNet](virtual-networks-migrate-to-regional-vnet.md).
>

## <a name="benefits"></a>Fördelar
* **Isolering**. VNet är helt isolerade från varandra. Det låter dig skapa sönderdelade nätverk för utveckling, testning och produktion som använder sig av samma CIDR-adressblock.
* **Åtkomst till offentligt Internet**. Alla IaaS-VM:ar och PaaS-rollinstanser i ett VNet har åtkomst till det offentliga Internet som standard. Du kan styra åtkomsten genom att använda nätverkssäkerhetsgrupper (NSG:er).
* **Åtkomst till VM:ar inom VNet**. PaaS-rollinstanser och IaaS-VM:ar kan köras i samma virtuella nätverk och de kan ansluta till varandra med privata IP-adresser även om de befinner sig på olika undernät utan att behöva konfigurera en gateway eller använda sig av offentliga IP-adresser.
* **Namnmatchning**. Azure erbjuder [intern namnmatchning](virtual-networks-name-resolution-for-vms-and-role-instances.md) för IaaS-VM:ar och PaaS-rollinstanser som finns distribuerade på ditt VNet. Du kan också distribuera dina egna DNS-servrar och konfigurera VNet att använda sig av dem.
* **Säkerhet**. Inkommande och utgående trafik från den virtuella datorn och PaaS-rollinstanserna i ett VNet kan kontrolleras med nätverkssäkerhetsgrupper.
* **Anslutning**. Virtuella nätverk kan anslutas till varandra med hjälp av nätverksgateways eller VNet-peering. Virtuella nätverk kan anslutas till lokala datacenter via VPN-nätverk för plats till plats eller Azure ExpressRoute. Mer information om VPN-anslutningar för plats till plats finns i artikeln om [VPN-gateways](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site-connections). Mer information om ExpressRoute finns i [Teknisk översikt för ExpressRoute](../expressroute/expressroute-introduction.md). Mer information om VNet-peering finns på sidan om [VNet-peering](virtual-network-peering-overview.md).

  > [!NOTE]
  > Se till att du skapar ett VNet innan du distribuerar några IaaS-VM:ar eller PaaS-rollinstanser till din Azure-miljö. ARM-baserade VM:ar kräver ett VNet och om du inte anger ett existerande VNet, kommer Azure att skapa ett standard-VNet som kan ha ett CIDR-adressblock som krockar med ditt lokala nätverk. Vilket gör det omöjligt för dig att ansluta ditt VNet till ditt lokala nätverk.
  >

## <a name="subnets"></a>Undernät
Ett undernät är ett IP-adressintervall i det virtuella nätverket. Du kan dela upp ett virtuellt nätverk i flera undernät av organisations- och säkerhetsskäl. VM:ar och PaaS-rollinstanser som distribuerats till undernät (samma eller olika) inom ett VNet, kan kommunicera med varandra utan övrig konfiguration. Du kan också konfigurera route-tabeller och NSG:er till ett undernät.

## <a name="ip-addresses"></a>IP-adresser
Det finns två typer av IP-adresser som tilldelas resurser i Azure: *offentliga* och *privata*. Offentliga IP-adresser gör att Azure-resurserna kan kommunicera med Internet och andra offentliga Azure-tjänster som [Azure Redis Cache](https://azure.microsoft.com/services/cache/) och [Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). Privata IP-adresser tillåter kommunikation mellan resurser i ett virtuellt nätverk, samt de som är anslutna via ett VPN-nätverk, utan att använda Internetdirigerbara IP-adresser.

Om du vill veta mer om IP-adresser i Azure kan du besöka [IP-adresser i virtuella nätverk](virtual-network-ip-addresses-overview-arm.md)

## <a name="azure-load-balancers"></a>Azure belastningsutjämnare
Virtuella datorer och molntjänster i ett virtuellt nätverk kan exponeras mot Internet med Azure belastningsutjämnare. Verksamhetsspecifika program som bara är internt riktade kan belastningsutjämnas med interna belastningsutjämnare.

* **Externa belastningsutjämnare**. Du kan använda en extern belastningsutjämnare för att tillhandahålla hög tillgänglighet för IaaS-VM:ar och PaaS-rollinstanser som nås via det offentliga Internet.
* **Interna belastningsutjämnare**. Du kan använda en intern belastningsutjämnare för att erbjuda hög tillgänglighet för IaaS-VM:ar och PaaS-rollinstanser som nås från andra tjänster i ditt VNet.

För att få veta mer om belastningsutjämning i Azure, kan du besöka [Översikt över belastningsutjämnare](../load-balancer/load-balancer-overview.md).

## <a name="network-security-groups-nsg"></a>Nätverkssäkerhetsgrupper (NSG)
Du kan skapa NSG:er för att kontrollera inkommande och utgående åtkomst till nätverksgränssnitt (NIC:ar), VM:ar och undernät. Varje NSG innehåller en eller flera regler som anger om trafik godkänns eller nekas baserat på käll-IP-adress, källport, mål-IP-adress och målport. Mer information om NSG:ar finns i [Vad är en nätverkssäkerhetsgrupp](virtual-networks-nsg.md).

## <a name="virtual-appliances"></a>Virtuella installationer
En virtuell installation är en VM på ditt VNet som kör en programvarubaserad installationsfunktion, som en brandvägg, WAN-optimering eller intrångsidentifiering. Du kan skapa en väg i Azure för att dirigera din VNet-trafik via en virtuell installation och använda dess funktioner.

NSG:er kan till exempel användas för att tillhandahålla säkerhet på ditt VNet. Dock erbjuder NSG:er lager 4 åtkomstkontrollistor (ACL) för inkommande och utgående paket. Om du vill använda en lager 7 säkerhetsmodell, behöver du använda en brandväggsinstallation.

Virtuella installationer beror på [användardefinierade vägar och IP-vidarebefordring](virtual-networks-udr-overview.md).

## <a name="limits"></a>Begränsningar
Det finns begränsningar för antalet virtuella nätverk som tillåts för en prenumeration, referera till [Azure nätverksbegränsningar](../azure-subscription-service-limits.md#networking-limits) för mer information.

## <a name="pricing"></a>Priser
Det finns inget extra kostnad för att använda Virtual Networks i Azure. De compute-instanser som startas inom VNet kommer att debiteras till standardpriser enligt [Priser för Azure VM](https://azure.microsoft.com/pricing/details/virtual-machines/). De [VPN-gatewayer](https://azure.microsoft.com/pricing/details/vpn-gateway/) och [offentliga IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses/) som används i VNet kommer att debiteras med standardpriser.

## <a name="next-steps"></a>Nästa steg
* [Skapa en VNet](virtual-networks-create-vnet-arm-pportal.md) och undernät.
* [Skapa en VM i VNet](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
* Läs mer om [NSG:er](virtual-networks-nsg.md).
* Läs mer om [användardefinierade vägar och IP-vidarebefordring](virtual-networks-udr-overview.md).



<!--HONumber=Feb17_HO4-->


