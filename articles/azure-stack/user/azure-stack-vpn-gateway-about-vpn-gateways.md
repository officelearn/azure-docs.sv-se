---
title: Om VPN gateway för Azure Stack | Microsoft Docs
description: Lär dig mer om och konfigurera VPN-gatewayer som du använder med Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/15/2019
ms.author: sethm
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 7bf7034d30a8aac187fb2eeae6569f2f495e4439
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327263"
---
# <a name="about-vpn-gateway-for-azure-stack"></a>Om VPN gateway för Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Innan du kan skicka nätverkstrafik mellan Azure-nätverk och lokala platser, måste du skapa en virtuell nätverksgateway för ditt virtuella nätverk.

En VPN-gateway är en typ av virtuell nätverksgateway som skickar krypterad trafik över en offentlig anslutning. Du kan använda VPN-gatewayer för att skicka trafik på ett säkert sätt mellan ett virtuellt nätverk i Azure Stack och ett virtuellt nätverk i Azure. Du kan också skicka trafiken på ett säkert sätt mellan ett virtuellt nätverk och ett annat nätverk som är ansluten till en VPN-enhet.

När du skapar en virtuell nätverksgateway anger du vilken typ av gateway som du vill skapa. Azure Stack stöd för en typ av virtuell nätverksgateway: den **Vpn** typen.

Varje virtuellt nätverk kan ha två virtuella nätverksgatewayer, men bara en av varje typ. Du kan skapa flera anslutningar till en enda VPN-gateway beroende på de inställningar som du väljer. Ett exempel är en konfiguration för anslutning till flera platser.

Innan du skapar och konfigurerar VPN-gatewayer för Azure Stack, granska de [överväganden för nätverk i Azure Stack](azure-stack-network-differences.md) att lära dig hur konfigurationer för Azure Stack skiljer sig från Azure.

>[!NOTE]
>I Azure, måste du dela upp bandbredd dataflödet för VPN-gateway SKU som du väljer i alla projekt som är anslutna till gatewayen. I Azure Stack tillämpas dock bandbreddsvärdet för VPN-gateway-SKU till varje anslutningsresurs som är ansluten till gatewayen.
>
> Exempel:
> * I Azure, kan de grundläggande VPN-gatewayen-SKU hantera cirka 100 Mbit/s för aggregerat dataflöde. Om du skapar två anslutningar till denna VPN-gateway och en anslutning använder 50 Mbit/s bandbredd, sedan 50 Mbit/s är tillgänglig för den andra anslutningen.
> * I Azure Stack *varje* anslutning till de grundläggande VPN-gatewayen-SKU tilldelas 100 Mbit/s för dataflödet.

## <a name="configuring-a-vpn-gateway"></a>Konfigurera en VPN-gateway

En anslutning för VPN-gateway är beroende av flera resurser som är konfigurerade med specifika inställningar. De flesta av de här resurserna kan konfigureras separat, men i vissa fall måste de konfigureras i en viss ordning.

### <a name="settings"></a>Inställningar

De inställningar som du väljer för varje resurs är viktiga för att skapa en lyckad anslutning.

Information om enskilda resurser och inställningar för en VPN-gateway finns i [om VPN gateway-inställningar för Azure Stack](azure-stack-vpn-gateway-settings.md). Den här artikeln hjälper dig att förstå:

* Gatewaytyper, VPN-typer och anslutningstyper.
* Gateway-undernät, lokala nätverksgatewayer och andra resursinställningar som du kan använda.

### <a name="deployment-tools"></a>Distributionsverktyg

Du kan skapa och konfigurera resurser med hjälp av ett konfigurationsverktyg, till exempel Azure portal. Senare kan du växla till ett annat verktyg som PowerShell för att konfigurera ytterligare resurser eller ändra befintliga resurser när så är tillämpligt. För närvarande kan konfigurera du inte alla resurser och resursinställningar på Azure-portalen. Anvisningarna i artiklarna för varje anslutningstopologi anger om ett specifikt konfigurationsverktyg behövs.

## <a name="connection-topology-diagrams"></a>Anslutningstopologi

Det finns olika konfigurationer för VPN gateway-anslutningar. Bestämma vilken konfiguration som passar bäst för dina behov. I följande avsnitt kan du visa information och Topologidiagram om följande VPN-gatewayanslutningar:

* tillgänglig distributionsmodell
* tillgängliga konfigurationsverktyg
* länkar som tar dig direkt till en artikel om en sådan finns

Diagrammen och beskrivningarna i följande avsnitt kan hjälpa dig att välja en anslutningstopologi som passar dina behov. Diagrammen visar de viktigaste grundläggande topologierna, men det är möjligt att skapa mer komplexa konfigurationer med diagrammen som vägledning.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Plats-till-plats och flera platser (IPsec/IKE VPN-tunnel)

### <a name="site-to-site"></a>Plats-till-plats

En *plats-till-plats* (S2S) VPN gateway-anslutning är en anslutning via IPsec/IKE (IKEv2) VPN-tunnel. Den här typen av anslutning kräver en VPN-enhet som finns lokalt och har tilldelats en offentlig IP-adress. Den här enheten får inte finnas bakom en NAT. S2S-anslutningar kan användas för konfigurationer mellan platser och för hybridkonfigurationer.

![Konfigurationsexempel för plats-till-plats VPN-anslutning](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>Flera platser

En *multisite* anslutningen är en variant av plats-till-plats-anslutning. Du skapar fler än en VPN-anslutning från din virtuella nätverksgateway, vanligtvis sker anslutningen till flera lokala platser. När du arbetar med flera anslutningar måste du använda en routningsbaserad VPN-typ (kallas en dynamisk gateway när du arbetar med klassiska virtuella nätverk). Eftersom varje virtuellt nätverk bara kan ha en VPN-gateway delar alla anslutningar via gatewayen på den tillgängliga bandbredden.

![Exempel på Azure VPN Gateway-anslutningar för flera platser](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>Gateway-SKU:er

När du skapar en virtuell nätverksgateway för Azure Stack kan ange du vilken gateway-SKU som du vill använda. I följande VPN-gateway SKU: er stöds:

* Basic
* Standard
* Höga prestanda

När du väljer en högre gateway-SKU, till exempel Standard än på Basic- eller hög prestanda över Standard eller Basic allokeras fler processorer och mer nätverksbandbredd till gatewayen. Gatewayen kan därför hantera större nätverksgenomflöde till det virtuella nätverket.

Azure Stack stöder inte gatewayen SKU som uteslutande används tillsammans med Express Route med ultrahöga prestanda.

Tänk på följande när du väljer SKU: N:

* Azure Stack har inte stöd för principbaserade gateways.
* Border Gateway Protocol (BGP) stöds inte på en grundläggande SKU.
* ExpressRoute VPN gateway samexisterande konfigurationer stöds inte i Azure Stack.
* Aktiv-aktiv S2S VPN gateway-anslutningar kan konfigureras på hög prestanda SKU endast.

## <a name="estimated-aggregate-throughput-by-sku"></a>Beräknat aggregerat dataflöde av SKU

I följande tabell visar gateway-typerna och beräknat aggregerat dataflöde efter gateway-SKU:

|   | VPN-Gateway, genomflöde *(1)* | VPN-Gateway, Max. IPsec-tunnlar *(2)* |
|-------|-------|-------|
|**Grundläggande SKU** ***(3)***    | 100 Mbit/s  | 20    |
|**Standard-SKU**       | 100 Mbit/s  | 20    |
|**Högpresterande SKU** | 200 Mbit/s    | 10    |

**Tabellen kommentarer:**

*Obs! (1)* -VPN-dataflöde är inte garanterad genomströmning för anslutningar mellan platser över Internet. Värdet utgör dock högsta möjliga genomflöde.  
*Obs! (2)* -Max tunnlar är det totala antalet per Azure Stack-distribution för alla prenumerationer.  
*Obs! (3)* -BGP-routning stöds inte för en grundläggande SKU.

>[!NOTE]
>Du kan skapa bara en plats-till-plats VPN-anslutning mellan två Azure Stack-distributioner. Detta beror på en begränsning i den plattform som tillåter endast en enda VPN-anslutning till samma IP-adress. Eftersom Azure Stack utnyttjar gateway för flera innehavare som använder en enda offentlig IP-adress för alla VPN-gatewayer i Azure Stack-system, kan det finnas bara en VPN-anslutning mellan två Azure Stack-system. Den här begränsningen gäller även för att ansluta fler än en plats-till-plats VPN-anslutning till VPN-gateway som använder en IP-adress. Azure Stack tillåter inte mer än en lokal gateway nätverksresurs som ska skapas med samma IP-adress.

## <a name="next-steps"></a>Nästa steg

[Konfigurationsinställningar för VPN-gateway för Azure Stack](azure-stack-vpn-gateway-settings.md)
