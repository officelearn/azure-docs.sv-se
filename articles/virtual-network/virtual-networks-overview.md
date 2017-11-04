---
title: "Virtuella Azure-nätverket | Microsoft Docs"
description: "Läs mer om Azure Virtual Network koncept och funktioner."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/23/2017
ms.author: jdial
ms.openlocfilehash: 6d6afd2b9b956138ed400fbd6cabd3b480fde0f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-virtual-network"></a>Azure Virtual Network

Tjänsten Azure Virtual Network kan du ansluta Azure-resurser på ett säkert sätt till varandra med virtuella nätverk (Vnet). Ett virtuellt nätverk är en representation av ditt eget nätverk i molnet. Ett virtuellt nätverk är en logisk isolering av Azure-molnet dedikerad till din prenumeration. Du kan också ansluta Vnet till ditt lokala nätverk. Följande bild visar några av funktionerna i Azure Virtual Network service:

![Nätverksdiagram](./media/virtual-networks-overview/virtual-network-overview.png)

Mer information om följande funktioner i Azure Virtual Network klickar du på funktionen:
- **[Isolering:](#isolation)**  Vnet isoleras från varandra. Du kan skapa separata Vnet för utveckling, testning och produktion som använder samma CIDR-Adressblock. Däremot kan du skapa flera virtuella nätverk som använder olika CIDR-Adressblock och ansluta nätverk tillsammans. Du kan dela ett VNet i flera undernät. Azure erbjuder intern namnmatchning för virtuella datorer och molntjänster rollinstanser ansluten till ett virtuellt nätverk. Du kan också konfigurera ett virtuellt nätverk för att använda dina egna DNS-servrar i stället för intern namnmatchning för Azure.
- **[Internet-anslutning:](#internet)**  alla Azure virtuella datorer (VM) och molntjänster rollinstanser ansluten till ett virtuellt nätverk har åtkomst till Internet, som standard. Du kan också aktivera inkommande åtkomst till specifika resurser efter behov.
- **[Azure-resurs-anslutningen:](#within-vnet)**  Azure-resurser som molntjänster och virtuella datorer kan anslutas till samma virtuella nätverk. Resurserna kan ansluta till varandra med privata IP-adresser, även om de finns i olika undernät. Azure tillhandahåller standardroutning mellan undernät, virtuella nätverk och lokala nätverk, så du behöver att konfigurera och hantera vägar.
- **[VNet-anslutningen:](#connect-vnets)**  Vnet kan anslutas till varandra, aktivera resurser som är anslutna till alla virtuella nätverk för att kommunicera med alla resurser för andra virtuella nätverk.
- **[Lokal anslutning:](#connect-on-premises)**  Vnet kan anslutas till lokalt nätverk via privata nätverksanslutningar mellan ditt nätverk och Azure, eller en plats-till-plats VPN-anslutning via Internet.
- **[Trafikfiltrering:](#filtering)**  molntjänster och Virtuella nätverkstrafik för rollen instanser inkommande och utgående kan filtreras efter källans IP-adress och port, mål-IP-adress och port och protokoll.
- **[Routning:](#routing)**  Alternativt kan du åsidosätta Azures standard routning genom att konfigurera egna vägar eller använda BGP-vägar via en nätverksgateway.

## <a name = "isolation"></a>Isolering av nätverk och segmentering

Du kan implementera flera Vnet i varje Azure [prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) och Azure [region](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region). Varje virtuellt nätverk är isolerad från andra Vnet. För varje virtuellt nätverk kan du:
- Ange en egen privata IP-adressutrymmet med hjälp av offentliga och privata (RFC 1918)-adresser. Azure tilldelar resurser som ansluten till VNet en privat IP-adress från det adressutrymme som du tilldelar.
- Segmentera VNet i en eller flera undernät och tilldela en del av VNet-adressutrymmet för varje undernät.
- Använd Azure-tillhandahållna namnmatchning eller ange egna DNS-server för användning av resurser som är anslutna till ett virtuellt nätverk. Mer information om namnmatchning i Vnet i [namnmatchning för virtuella datorer och molntjänster](virtual-networks-name-resolution-for-vms-and-role-instances.md) artikel.

## <a name = "internet"></a>Ansluta till Internet
Alla resurser som är ansluten till ett virtuellt nätverk har utgående anslutning till Internet som standard. Privata IP-adressen för resursen är källan nätverksadress översättas (SNAT) till en offentlig IP-adress av Azure-infrastrukturen. Om du vill veta mer om utgående Internetanslutning kan du läsa den [förstå utgående anslutningar i Azure](..\load-balancer\load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json#standalone-vm-with-no-instance-level-public-ip-address) artikel. Du kan ändra standard anslutningen genom att implementera anpassad Routning och filtrera trafik.

Att kommunicera inkommande Azure-resurser från Internet eller kommunicera utgående till Internet utan SNAT tilldelas en resurs en offentlig IP-adress. Mer information om den offentliga IP-adresser i [offentliga IP-adresser](virtual-network-public-ip-address.md) artikel.

## <a name="within-vnet"></a>Anslut Azure-resurser
Du kan ansluta flera Azure-resurser till ett virtuellt nätverk, till exempel virtuella datorer (VM), Cloud Services, Apptjänstmiljöer och Skalningsuppsättningar i virtuella datorer. Virtuella datorer ansluta till ett undernät i ett virtuellt nätverk via ett nätverksgränssnitt (NIC). Mer information om nätverkskort på [nätverksgränssnitt](virtual-network-network-interface.md) artikel.

## <a name="connect-vnets"></a>Ansluta virtuella nätverk

Du kan ansluta Vnet till varandra, aktivera resurser som är anslutna till något virtuellt nätverk att kommunicera med varandra via Vnet. Du kan använda ett eller båda av följande alternativ för att ansluta Vnet till varandra:
- **Peering:** gör att resurser som anslutits till olika virtuella Azure-nätverk inom samma Azure-plats att kommunicera med varandra. Bandbredd och fördröjning mellan till Vnet är samma som resurserna som är anslutna till samma virtuella nätverk. Mer information om peering den [virtuella nätverk peering](virtual-network-peering-overview.md) artikel.
- **VNet-till-VNet-anslutningen:** gör att resurser som anslutits till olika Azure-VNet i samma eller olika Azure platser. Till skillnad från peering, är bandbredd eftersom begränsade mellan Vnet trafik måste gå genom en Azure VPN-Gateway. Mer information om hur du ansluter Vnet med ett VNet-till-VNet-anslutningen i [konfigurera VNet-till-VNet-anslutningen](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artikel.

## <a name="connect-on-premises"></a>Ansluta till ett lokalt nätverk

Du kan ansluta dina lokala nätverk till ett virtuellt nätverk med hjälp av följande alternativ:
- **Punkt-till-plats virtuellt privat nätverk (VPN):** upprättas mellan en enda dator som är ansluten till nätverket och VNet. Den här anslutningen är bra om du precis har börjat med Azure, eller för utvecklare, eftersom det krävs lite eller ingen ändringar i ditt befintliga nätverk. Anslutningen använder SSTP-protokollet för att tillhandahålla krypterad kommunikation via Internet mellan datorn och VNet. Svarstiden för en punkt-till-plats-VPN är oförutsägbart, eftersom trafiken färdas genom Internet.
- **Plats-till-plats-VPN:** mellan din VPN-enhet och en Azure VPN-Gateway. Den här anslutningstypen gör att alla lokala resurser som du har behörighet att komma åt ett VNet. Anslutningen är en IPSec/IKE-VPN som tillhandahåller krypterad kommunikation via Internet mellan din lokala enhet och Azure VPN-gatewayen. Svarstiden för en plats-till-plats-anslutning är oförutsägbart, eftersom trafiken färdas genom Internet.
- **Azure ExpressRoute:** upprättas mellan ditt nätverk och Azure, via en ExpressRoute-partner. Den här anslutningen är privat. Trafik inte går över Internet. Svarstid för en ExpressRoute-anslutning är förutsägbar, eftersom trafiken inte sker via Internet.

Mer information om alla tidigare anslutningsalternativ den [anslutning Topologidiagram](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams) artikel.

## <a name="filtering"></a>Filtrera nätverkstrafik
Du kan filtrera nätverkstrafiken mellan undernät med ett eller båda av följande alternativ:
- **Nätverkssäkerhetsgrupper (NSG):** varje NSG kan innehålla flera inkommande och utgående säkerhetsregler som gör det möjligt att filtrera trafik genom käll- och IP-adress, port och protokoll. Du kan använda en NSG för varje nätverkskort på en virtuell dator. Du kan också använda en NSG till undernätet för ett nätverkskort eller andra Azure-resurs är ansluten till. Mer information om NSG: er i [Nätverkssäkerhetsgrupper](virtual-networks-nsg.md) artikel.
- **Nätverks-virtuella installationer (NVA):** en NVA är en virtuell dator kör programvara som utför en funktion i nätverket, till exempel en brandvägg. Visa en lista över tillgängliga NVAs i den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). NVAs är också tillgängliga som ger WAN-optimering och andra nätverksenheter trafik funktioner. NVAs används vanligtvis med användardefinierade eller BGP-vägar. Du kan också använda en NVA för att filtrera trafik mellan virtuella nätverk.

## <a name="routing"></a>Dirigera nätverkstrafik

Azure skapar routningstabeller som gör att resurser som är anslutna till alla undernät i alla virtuella nätverk för att kommunicera med varandra som standard. Du kan implementera ett eller båda av följande alternativ för att åsidosätta standardvägar Azure skapar:
- **Användardefinierade vägar:** kan du skapa anpassade routningstabeller med vägar som styr där trafik dirigeras till för varje undernät. Mer information om användardefinierade vägar finns i artikeln [Användardefinierade vägar](virtual-networks-udr-overview.md).
- **BGP-vägar:** om du ansluter ditt VNet till ditt lokala nätverk med hjälp av en Azure VPN-Gateway eller ExpressRoute-anslutning du sprida BGP-vägar till din Vnet.

## <a name="pricing"></a>Prissättning

Det är gratis för virtuella nätverk, undernät, vägtabeller eller nätverket säkerhetsgrupper. Utgående Internet-bandbreddsanvändning, offentliga IP-adresser, peering virtuellt nätverk, VPN-gatewayer och ExpressRoute varje har olika priser strukturer. Visa den [för virtuella nätverk](https://azure.microsoft.com/pricing/details/virtual-network), [VPN-Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway), och [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) priser sidor för mer information.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

Vanliga frågor och svar om virtuella nätverk finns i [virtuella nätverk vanliga frågor och svar](virtual-networks-faq.md) artikel.


## <a name="next-steps"></a>Nästa steg

- Skapa din första virtuella nätverk och ansluta ett fåtal virtuella datorer, genom att slutföra stegen i den [skapa din första virtuella nätverket](virtual-network-get-started-vnet-subnet.md) artikel.
- Skapa en punkt-till-plats-anslutning till ett virtuellt nätverk genom att slutföra stegen i den [konfigurerar en punkt-till-plats-anslutning](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artikel.
- Lär dig mer om den andra nyckeln [nätverk funktioner](../networking/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure.
