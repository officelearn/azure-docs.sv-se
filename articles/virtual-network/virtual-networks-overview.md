---
title: Azure Virtual Network | Microsoft Docs
description: Läs mer om Azure Virtual Network-koncept och -funktioner.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 8/8/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: fe84dfcef2a5dad1c170592f933638b984e16a05
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44717037"
---
# <a name="what-is-azure-virtual-network"></a>Vad är Azure Virtual Network?

Azure Virtual Network gör att många typer av Azure-resurser, till exempel virtuella Azure-datorer (VM), kan kommunicera säkert med varandra, Internet och lokala nätverk. Azure Virtual Network innehåller följande viktiga funktioner:

## <a name="isolation-and-segmentation"></a>Isolering och segmentering

Du kan implementera flera virtuella nätverk i varje Azure-[prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) och Azure-[region](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region). Varje virtuellt nätverk är isolerat från andra virtuella nätverk. För varje virtuellt nätverk kan du:
- Ange ett anpassat privat IP-adressutrymme med offentliga och privata adresser (RFC 1918). Azure tilldelar resurser i ett virtuellt nätverk en privat IP-adress från det adressutrymme som du tilldelar.
- Segmentera det virtuella nätverket i ett eller flera undernät och allokera en del av det virtuella nätverkets adressutrymme till varje undernät.
- Använda Azure-tillhandahållen namnmatchning, eller ange en egen DNS-server, för användning av resurser i ett virtuellt nätverk.

## <a name="communicate-with-the-internet"></a>Kommunicera med Internet

Alla resurser i ett virtuellt nätverk kan som standard kommunicera utgående till Internet. Du kan kommunicera inkommande trafik till en resurs genom att tilldela en offentlig IP-adress eller en offentlig lastbalanserare. Du kan också använda en offentlig IP-adress eller en offentlig lastbalanserare för att hantera utgående anslutningar.  Mer information om utgående anslutningar i Azure finns i avsnitten om [utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md), [offentliga IP-adresser](virtual-network-public-ip-address.md) och [lastbalanseraren](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>Om du bara använder en intern [standardlastbalanserare](../load-balancer/load-balancer-standard-overview.md), är utgående anslutningar inte tillgängliga förrän du definierar hur du vill att [utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md) ska fungera med en offentlig IP-adress på instansnivå eller en offentlig lastbalanserare.

## <a name="communicate-between-azure-resources"></a>Kommunicera mellan Azure-resurser

Azure-resurser kommunicerar säkert med varandra på något av följande sätt:

- **Via ett virtuellt nätverk**: Du kan distribuera virtuella datorer och flera andra typer av Azure-resurser till ett virtuellt nätverk, som Azure App Service-miljöer, AKS (Azure Kubernetes Service) och Azure Virtual Machine Scale Sets. En fullständig lista över Azure-resurser som du kan distribuera till ett virtuellt nätverk finns i [Tjänstintegration för virtuella nätverk](virtual-network-for-azure-services.md). 
- **Via en tjänstslutpunkt för virtuella nätverk**: Utöka det privata adressutrymmet för det virtuella nätverket och identiteten för det virtuella nätverket till Azure-tjänstresurser, till exempel Azure Storage-konton och Azure SQL-databaser, via en direkt anslutning. Med tjänstslutpunkter kan du skydda dina kritiska Azure-tjänstresurser till endast ett virtuellt nätverk. Läs mer i [Översikt över tjänstslutpunkter för virtuella nätverk](virtual-network-service-endpoints-overview.md).
 
## <a name="communicate-with-on-premises-resources"></a>Kommunicera med lokala resurser

Du kan ansluta lokala datorer och nätverk till ett virtuellt nätverk med hjälp av valfri kombination av följande alternativ:

- **Punkt-till-plats-VPN (virtuellt privat nätverk):** Upprättas mellan ett virtuellt nätverk och en dator i ditt nätverk. Anslutningen måste konfigureras för varje dator som du vill ansluta till ett virtuellt nätverk. Den här anslutningstypen är bra om du precis har börjat med Azure, eller för utvecklare, eftersom det krävs få eller inga ändringar i ditt befintliga nätverk. Kommunikationen mellan datorn och ett virtuellt nätverk skickas genom en krypterad tunnel via Internet. Läs mer i [Punkt-till-plats-VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S).
- **Plats-till-plats-VPN:** Upprättas mellan den lokala VPN-enheten och Azure VPN Gateway som distribueras i ett virtuellt nätverk. Med den här anslutningstypen kan alla lokala resurser som du ger behörighet få åtkomst till ett virtuellt nätverk. Kommunikationen mellan den lokala VPN-enheten och en Azure VPN-gateway skickas genom en krypterad tunnel via Internet. Läs mer i [Plats-till-plats-VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute:** Upprättas mellan ditt nätverk och Azure, via en ExpressRoute-partner. Den här anslutningen är privat. Trafiken går inte via Internet. Läs mer i [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute).

## <a name="filter-network-traffic"></a>Filtrera nätverkstrafik
Du kan filtrera nätverkstrafik mellan undernät med ett eller båda av följande alternativ:
- **Nätverkssäkerhetsgrupper:** En nätverkssäkerhetsgrupp kan innehålla flera inkommande och utgående säkerhetsregler som gör det möjligt att filtrera trafik till och från resurser efter källans och målets IP-adress, port och protokoll. Läs mer i [Nätverkssäkerhetsgrupper](security-overview.md#network-security-groups).
- **Virtuella nätverksinstallationer:** En virtuell nätverksinstallation är en virtuell dator som utför en nätverksfunktion, till exempel en brandvägg, WAN-optimering eller annan nätverksfunktion. En lista över tillgängliga virtuella nätverksinstallationer som du kan distribuera i ett virtuellt nätverk finns i [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

## <a name="route-network-traffic"></a>Dirigera nätverkstrafik

Azure dirigerar trafik mellan undernät, anslutna virtuella nätverk, lokala nätverk och Internet, som standard. Du kan implementera ett eller båda av följande alternativ för att åsidosätta de standardvägar som Azure skapar:
- **Routningstabeller:** Du kan skapa anpassade routningstabeller med vägar som styr vart trafik dirigeras för varje undernät. Läs mer om [routningstabeller](virtual-networks-udr-overview.md#user-defined).
- **BGP-vägar (Border Gateway Protocol):** Om du ansluter ditt virtuella nätverk till ditt lokala nätverk med hjälp av en Azure VPN Gateway- eller ExpressRoute-anslutning kan du sprida de lokala BGP-vägarna till de virtuella nätverken. Lär dig mer om hur du använder BGP med [Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

## <a name="connect-virtual-networks"></a>Ansluta virtuella nätverk

Du kan ansluta virtuella nätverk till varandra, så att resurser i de virtuella nätverken kan kommunicera med varandra, med hjälp av peerkoppling av virtuella nätverk. De virtuella nätverken du ansluter kan finnas i samma, eller olika, Azure-regioner. Läs mer i [Peerkoppling av virtuella nätverk](virtual-network-peering-overview.md).

## <a name="next-steps"></a>Nästa steg

Nu har du en överblick av Azure Virtual Network. Kom igång med ett virtuellt nätverk genom att skapa ett, distribuera några virtuella datorer till det och kommunicera mellan de virtuella datorerna. Mer information finns i snabbstarten [Skapa ett virtuellt nätverk](quick-create-portal.md).
