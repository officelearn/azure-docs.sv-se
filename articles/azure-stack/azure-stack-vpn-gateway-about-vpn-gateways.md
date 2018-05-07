---
title: Om VPN-gateway för Azure-Stack | Microsoft Docs
description: Lär dig mer om och konfigurera VPN-gatewayer som du använder med Azure-stacken.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/01/2017
ms.author: brenduns
ms.openlocfilehash: 7e489db0d9a65b850df41360ce11616d518c5265
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="about-vpn-gateway-for-azure-stack"></a>Om VPN-gateway för Azure-Stack
*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*


Innan du kan skicka nätverkstrafik mellan dina virtuella Azure-nätverket och den lokala platsen, måste du skapa en virtuell nätverksgateway för det virtuella nätverket.

En VPN-gateway är en typ av virtuell nätverksgateway som skickar krypterad trafik över en offentlig anslutning. Du kan använda VPN-gatewayer för att skicka trafik på ett säkert sätt mellan ett virtuellt nätverk i Azure-stacken och ett virtuellt nätverk i Azure. Du kan också skicka trafik på ett säkert sätt mellan ett virtuellt nätverk och ett annat nätverk som är ansluten till en VPN-enhet.

När du skapar en virtuell nätverksgateway anger du vilken typ av gateway som du vill skapa. Azure-stacken stöd för en typ av virtuell nätverksgateway: 'Vpn-typen.

Varje virtuellt nätverk kan ha två virtuella nätverksgatewayer, men bara en av varje typ. Du kan skapa flera anslutningar till en enda VPN-gateway beroende på de inställningar som du väljer. Ett exempel är en konfiguration med flera plats-anslutning.

> [!NOTE]
> I Azure, måste bandbredden genomströmning för VPN-Gateway-SKU som du väljer delas över alla anslutningar som är anslutna till den.  I Azure-stacken används värdet för bandbredd för VPN-Gateway-SKU till varje anslutning resurs som är ansluten till den.     

> Till exempel i Azure grundläggande VPN-Gateway-SKU kan hantera cirka 100 Mbps sammanställda genomflöde.  Om du skapar två anslutningar till denna VPN-Gateway och en anslutning använder 50 Mbit/s bandbredd, sedan 50 Mbit/s är tillgänglig för den andra anslutningen.   

> I Azure-stacken *varje* anslutning till grundläggande VPN-Gateway-SKU hämtar allokerade 100 Mbit/s genomströmning.

## <a name="configuring-a-vpn-gateway"></a>Konfigurera en VPN Gateway
En anslutning för VPN-gateway är beroende av flera resurser som är konfigurerade med specifika inställningar. De flesta resurserna kan konfigureras separat, även om de måste konfigureras i en viss ordning i vissa fall.

### <a name="settings"></a>Inställningar
De inställningar som du väljer för varje resurs är viktiga för att skapa en lyckad anslutning. Information om enskilda resurser och inställningar för VPN-Gateway finns [om VPN-gateway-inställningar för Azure-stacken](azure-stack-vpn-gateway-settings.md). Du kan hitta information som hjälper dig att förstå gateway-typer, VPN-typer, anslutningstyper, gateway-undernät, lokala nätverksgatewayer och andra resursinställningar som du kanske vill.

### <a name="deployment-tools"></a>Distributionsverktyg
Du kan skapa och konfigurera resurser med hjälp av en konfiguration med till exempel Azure-portalen. Senare kan du växla till ett annat verktyg som PowerShell för att konfigurera ytterligare resurser eller ändra befintliga resurser när så är tillämpligt. För närvarande går det inte att konfigurera alla resurser och resursinställningar på Azure Portal. Anvisningarna i artiklarna för varje anslutningstopologi anger om ett specifikt konfigurationsverktyg behövs.

## <a name="connection-topology-diagrams"></a>Topologidiagram för anslutning
Det är viktigt att känna till att det finns olika konfigurationer för VPN-gatewayanslutningar. Bestämma vilken konfiguration som bäst passar dina behov. I avsnitten nedan kan du se information och topologidiagram om följande VPN-gatewayanslutningar: följande avsnitt innehåller tabeller som visar:

- tillgänglig distributionsmodell
- tillgängliga konfigurationsverktyg
- länkar som tar dig direkt till en artikel om en sådan finns

Diagram och beskrivningar i följande avsnitt kan hjälpa dig att välja en anslutning topologi för att matcha dina behov. Diagrammen visar de viktigaste grundläggande topologierna, men du kan skapa mer komplexa konfigurationer och använda diagrammen som utgångspunkt.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Plats-till-plats och flera platser (IPsec/IKE VPN-tunnel)
### <a name="site-to-site"></a>Plats-till-plats
En plats-till-plats-anslutning (S2S) för VPN-gateway är en anslutning via en VPN-tunnel med IPsec/IKE (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats en offentlig IP-adress och som inte finns bakom en NAT. S2S-anslutningar kan användas för konfigurationer mellan platser och för hybridkonfigurationer.    
![Konfigurationsexempel för plats-till-plats VPN-anslutning](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>Flera platser
Den här typen av anslutning är en variant av plats-till-plats-anslutningen. Du skapar fler än en VPN-anslutning från din virtuella nätverksgateway, vanligtvis sker anslutningen till flera lokala platser. När du arbetar med flera anslutningar måste du använda en RouteBased VPN-typ (kallas även en ”dynamisk gateway” för klassiska virtuella nätverk). Eftersom varje virtuellt nätverk bara kan ha en VPN-gateway delar alla anslutningar via gatewayen på den tillgängliga bandbredden. Det här kallas ofta en ”anslutning till flera platser”.   
![Exempel på Azure VPN Gateway-anslutningar för flera platser](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)



## <a name="gateway-skus"></a>Gateway-SKU:er
När du skapar en virtuell nätverksgateway för Azure-stacken ange gateway SKU som du vill använda. Följande VPN-gateway SKU stöds:
- Basic
- Standard
- HighPerformance

När du väljer en högre gateway SKU som Standard över Basic eller HighPerformance över Standard- eller Basic är mer CPU och nätverkets bandbredd allokerade till gatewayen. Gatewayen stöder därför högre dataflödet i nätverket till det virtuella nätverket.

Azure-stacken stöder inte UltraPerformance gateway SKU som uteslutande används tillsammans med Expressroute.

Tänk på följande när du väljer SKU:
- Azure-stacken har inte stöd för principbaserad gateways.
- Border Gateway Protocol (BGP) stöds inte på den grundläggande SKU: N.
- ExpressRoute-VPN-Gateway samexistera konfigurationer stöds inte i Azure-stacken
- S2S VPN Gateway-anslutningar av typen aktiv-aktiv kan enbart konfigureras på HighPerformance SKU.

## <a name="estimated-aggregate-throughput-by-sku"></a>Beräknat aggregerat dataflöde av SKU
Följande tabell visar gateway-typerna och beräknat aggregerat dataflöde efter gateway-SKU.

|   | VPN Gateway-genomströmning *(1)* | VPN-Gateway, Max. IPsec-tunnlar *(2)* |
|-------|-------|-------|
|**Grundläggande SKU** ***(3)***    | 100 Mbit/s  | 10    |
|**Standard-SKU**       | 100 Mbit/s  | 10    |
|**Högpresterande SKU** | 200 Mbit/s    | 5 |
***(1)***  VPN-genomströmning är inte en garanterad genomströmning för anslutningar mellan platser över Internet. Värdet utgör dock högsta möjliga genomflöde.  
***(2)***  Max tunnlar talet är det totala antalet per distribution av Azure-Stack för alla prenumerationer.  
***(3)***  BGP stöds inte för grundläggande SKU: N.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [inställningar för VPN-gatewayer](azure-stack-vpn-gateway-settings.md) för Azure-stacken.
