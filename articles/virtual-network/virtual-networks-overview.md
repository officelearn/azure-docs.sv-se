---
title: Azure Virtual Network | Microsoft Docs
description: Läs mer om Azure Virtual Network-koncept och -funktioner.
services: virtual-network
documentationcenter: na
author: anavinahar
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: anavin
ms.openlocfilehash: 967d391d4ac9a9704688dce9636d9a71b2002549
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879368"
---
# <a name="what-is-azure-virtual-network"></a>Vad är Azure Virtual Network?

Azure Virtual Network (VNet) är den grundläggande byggstenen för ditt privata nätverk i Azure. VNet gör det möjligt för många typer av Azure-resurser, till exempel Virtuella Azure-datorer (VM), att kommunicera säkert med varandra, internet och lokala nätverk. VNet liknar ett traditionellt nätverk som du skulle arbeta i ditt eget datacenter, men medför ytterligare fördelar med Azures infrastruktur som skala, tillgänglighet och isolering.

## <a name="vnet-concepts"></a>VNet-begrepp

- **Adressutrymme:** När du skapar ett virtuella nätverk måste du ange ett anpassat privat IP-adressutrymme med offentliga och privata (RFC 1918) adresser. Azure tilldelar resurser i ett virtuellt nätverk en privat IP-adress från det adressutrymme som du tilldelar. Om du till exempel distribuerar en virtuell dator i ett virtuellt nätverk med adressutrymme, 10.0.0.0/16, tilldelas den virtuella datorn en privat IP som 10.0.0.4.
- **Undernät:** Med undernät kan du segmentera det virtuella nätverket i ett eller flera undernätverk och allokera en del av det virtuella nätverkets adressutrymme till varje undernät. Du kan sedan distribuera Azure-resurser i ett visst undernät. Precis som i ett traditionellt nätverk kan du i undernät segmentera ditt virtuella nätverks adressutrymme i segment som är lämpliga för organisationens interna nätverk. Detta förbättrar också adressfördelningens effektivitet. Du kan skydda resurser i undernät med hjälp av nätverkssäkerhetsgrupper. Mer information finns i [Säkerhetsgrupper](security-overview.md).
- **Regioner**: VNet är begränsat till en enda region/plats. Flera virtuella nätverk från olika regioner kan dock anslutas tillsammans med virtual network peering.
- **Prenumeration:** VNet är begränsat till en prenumeration. Du kan implementera flera virtuella nätverk i varje Azure-[prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) och Azure-[region](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region).

## <a name="best-practices"></a>Bästa praxis

När du bygger ditt nätverk i Azure är det viktigt att tänka på följande universella designprinciper:

- Kontrollera att adressutrymmen inte överlappar varandra. Kontrollera att ditt VNet-adressutrymme (CIDR-block) inte överlappar organisationens andra nätverksintervall.
- Dina undernät bör inte täcka hela adressutrymmet på det virtuella nätverket. Planera i förväg och reservera lite adressutrymme för framtiden.
- Vi rekommenderar att du har färre stora virtuella nätverk än flera små virtuella nätverk. Detta förhindrar hanteringskostnader.
- Skydda ditt virtuella nätverk genom att tilldela NSG:er (Network Security Groups) till undernäten under dem.

## <a name="communicate-with-the-internet"></a>Kommunicera med Internet

Alla resurser i ett virtuella nätverk kan kommunicera utgående till internet, som standard. Du kan kommunicera inkommande trafik till en resurs genom att tilldela en offentlig IP-adress eller en offentlig lastbalanserare. Du kan också använda en offentlig IP-adress eller en offentlig lastbalanserare för att hantera utgående anslutningar.  Mer information om utgående anslutningar i Azure finns i avsnitten om [utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md), [offentliga IP-adresser](virtual-network-public-ip-address.md) och [lastbalanseraren](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>Om du bara använder en intern [standardlastbalanserare](../load-balancer/load-balancer-standard-overview.md), är utgående anslutningar inte tillgängliga förrän du definierar hur du vill att [utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md) ska fungera med en offentlig IP-adress på instansnivå eller en offentlig lastbalanserare.

## <a name="communicate-between-azure-resources"></a>Kommunicera mellan Azure-resurser

Azure-resurser kommunicerar säkert med varandra på något av följande sätt:

- **Via ett virtuellt nätverk**: Du kan distribuera virtuella datorer och flera andra typer av Azure-resurser till ett virtuellt nätverk, som Azure App Service-miljöer, AKS (Azure Kubernetes Service) och Azure Virtual Machine Scale Sets. En fullständig lista över Azure-resurser som du kan distribuera till ett virtuellt nätverk finns i [Tjänstintegration för virtuella nätverk](virtual-network-for-azure-services.md).
- **Via en tjänstslutpunkt för virtuellt nätverk**: Utöka det privata adressutrymmet för det virtuella nätverket och identiteten för det virtuella nätverket till Azure-tjänstresurser, till exempel Azure Storage-konton och Azure SQL-databaser, via en direkt anslutning. Med tjänstslutpunkter kan du skydda dina kritiska Azure-tjänstresurser till endast ett virtuellt nätverk. Läs mer i [Översikt över tjänstslutpunkter för virtuella nätverk](virtual-network-service-endpoints-overview.md).
- **Genom VNet Peering:** Du kan ansluta virtuella nätverk till varandra, vilket gör att resurser i antingen virtuellt nätverk för att kommunicera med varandra, med hjälp av virtuell nätverks peering. De virtuella nätverken du ansluter kan finnas i samma, eller olika, Azure-regioner. Läs mer i [Peerkoppling av virtuella nätverk](virtual-network-peering-overview.md).

## <a name="communicate-with-on-premises-resources"></a>Kommunicera med lokala resurser

Du kan ansluta lokala datorer och nätverk till ett virtuellt nätverk med hjälp av valfri kombination av följande alternativ:

- **Punkt-till-plats-VPN (virtuellt privat nätverk):** Upprättas mellan ett virtuellt nätverk och en dator i ditt nätverk. Anslutningen måste konfigureras för varje dator som du vill ansluta till ett virtuellt nätverk. Den här anslutningstypen är bra om du precis har börjat med Azure, eller för utvecklare, eftersom det krävs få eller inga ändringar i ditt befintliga nätverk. Kommunikationen mellan datorn och ett virtuellt nätverk skickas genom en krypterad tunnel via Internet. Läs mer i [Punkt-till-plats-VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S).
- **Plats-till-plats-VPN:** Upprättas mellan den lokala VPN-enheten och Azure VPN Gateway som distribueras i ett virtuellt nätverk. Med den här anslutningstypen kan alla lokala resurser som du ger behörighet få åtkomst till ett virtuellt nätverk. Kommunikationen mellan den lokala VPN-enheten och en Azure VPN-gateway skickas genom en krypterad tunnel via Internet. Läs mer i [Plats-till-plats-VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute:** Upprättas mellan ditt nätverk och Azure, via en ExpressRoute-partner. Den här anslutningen är privat. Trafiken går inte via Internet. Läs mer i [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute).

## <a name="filter-network-traffic"></a>Filtrera nätverkstrafik

Du kan filtrera nätverkstrafik mellan undernät med ett eller båda av följande alternativ:

- **Säkerhetsgrupper:** Nätverkssäkerhetsgrupper och programsäkerhetsgrupper kan innehålla flera inkommande och utgående säkerhetsregler som gör att du kan filtrera trafik till och från resurser efter käll- och mål-IP-adress, port och protokoll. Mer information finns i [Nätverkssäkerhetsgrupper](security-overview.md#network-security-groups) eller [Programsäkerhetsgrupper](security-overview.md#application-security-groups).
- **Virtuella nätverksinstallationer:** En virtuell nätverksinstallation är en virtuell dator som utför en nätverksfunktion, till exempel en brandvägg, WAN-optimering eller annan nätverksfunktion. En lista över tillgängliga virtuella nätverksinstallationer som du kan distribuera i ett virtuellt nätverk finns i [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

## <a name="route-network-traffic"></a>Dirigera nätverkstrafik

Azure dirigerar trafik mellan undernät, anslutna virtuella nätverk, lokala nätverk och Internet, som standard. Du kan implementera ett eller båda av följande alternativ för att åsidosätta de standardvägar som Azure skapar:

- **Routningstabeller:** Du kan skapa anpassade routningstabeller med vägar som styr vart trafik dirigeras för varje undernät. Läs mer om [routningstabeller](virtual-networks-udr-overview.md#user-defined).
- **BGP-vägar (Border Gateway Protocol):** Om du ansluter ditt virtuella nätverk till ditt lokala nätverk med hjälp av en Azure VPN Gateway- eller ExpressRoute-anslutning kan du sprida de lokala BGP-vägarna till de virtuella nätverken. Lär dig mer om hur du använder BGP med [Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

## <a name="virtual-network-integration-for-azure-services"></a>Integrering av virtuella nätverk för Azure-tjänster

Genom att integrera Azure-tjänster i ett virtuellt Azure-nätverk kan privat åtkomst till tjänsten från virtuella datorer eller beräkningsresurser i det virtuella nätverket.
Du kan integrera Azure-tjänster i ditt virtuella nätverk med följande alternativ:
- Distribuera [dedikerade instanser av tjänsten](virtual-network-for-azure-services.md) till ett virtuellt nätverk. Tjänsterna kan sedan nås privat i det virtuella nätverket och från lokala nätverk.
- Använda [Privat länk](../private-link/private-link-overview.md) för att komma åt privat en specifik instans av tjänsten från ditt virtuella nätverk och från lokala nätverk.
- Du kan också komma åt tjänsten med offentliga slutpunkter genom att utöka ett virtuellt nätverk till tjänsten, via [tjänstenslutpunkter](virtual-network-service-endpoints-overview.md). Tjänstslutpunkter gör att tjänstresurser kan skyddas till det virtuella nätverket.
 

## <a name="azure-vnet-limits"></a>Azure VNet-gränser

Det finns vissa gränser för hur många Azure-resurser du kan distribuera. De flesta Azure-nätverksgränser har högsta möjliga värden. Du kan dock [öka vissa nätverksgränser](../azure-portal/supportability/networking-quota-requests.md) enligt vad som anges på [sidan VNet-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits). 

## <a name="pricing"></a>Prissättning

Det finns ingen avgift för att använda Azure VNet, det är gratis. Standardavgifter gäller för resurser, till exempel virtuella datorer (VMs) och andra produkter. Mer information finns i [VNet-priser](https://azure.microsoft.com/pricing/details/virtual-network/) och [Azure-priskalkylatorn](https://azure.microsoft.com/pricing/calculator/).

## <a name="next-steps"></a>Nästa steg

 Kom igång med ett virtuellt nätverk genom att skapa ett, distribuera några virtuella datorer till det och kommunicera mellan de virtuella datorerna. Mer information finns i snabbstarten [Skapa ett virtuellt nätverk](quick-create-portal.md).
