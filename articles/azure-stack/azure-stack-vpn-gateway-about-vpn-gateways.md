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
ms.date: 07/02/2018
ms.author: sethm
ms.openlocfilehash: 860f1181a84ef87fe0e66c2746b76b800a326354
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52496205"
---
# <a name="about-vpn-gateway-for-azure-stack"></a>Om VPN gateway för Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Innan du kan skicka nätverkstrafik mellan Azure-nätverk och lokala platser, måste du skapa en virtuell nätverksgateway för ditt virtuella nätverk.

En VPN-gateway är en typ av virtuell nätverksgateway som skickar krypterad trafik över en offentlig anslutning. Du kan använda VPN-gatewayer för att skicka trafik på ett säkert sätt mellan ett virtuellt nätverk i Azure Stack och ett virtuellt nätverk i Azure. Du kan också skicka trafiken på ett säkert sätt mellan ett virtuellt nätverk och ett annat nätverk som är ansluten till en VPN-enhet.

När du skapar en virtuell nätverksgateway anger du vilken typ av gateway som du vill skapa. Azure Stack stöd för en typ av virtuell nätverksgateway i **Vpn** typen.

Varje virtuellt nätverk kan ha två virtuella nätverksgatewayer, men bara en av varje typ. Du kan skapa flera anslutningar till en enda VPN-gateway beroende på de inställningar som du väljer. Ett exempel är en konfiguration för anslutning till flera platser.

Innan du skapar och konfigurerar VPN-gatewayer för Azure Stack, granska de [överväganden för nätverk i Azure Stack](user/azure-stack-network-differences.md) att lära dig hur konfigurationer för Azure Stack skiljer sig från Azure.

>[!NOTE]
>I Azure, måste du dela upp bandbredd dataflödet för VPN-gateway SKU som du väljer i alla projekt som är anslutna till gatewayen. Men i Azure Stack, bandbreddsvärdet för VPN-gateway SKU används för varje anslutning-resurs som är ansluten till gatewayen.
>
> Exempel:
> * I Azure, kan den grundläggande VPN Gateway-SKU hantera cirka 100 Mbit/s för aggregerat dataflöde. Om du skapar två anslutningar till denna VPN-Gateway och en anslutning använder 50 Mbit/s bandbredd, sedan 50 Mbit/s är tillgänglig för den andra anslutningen.
> * I Azure Stack *varje* anslutning till den grundläggande VPN Gateway-SKU tilldelas 100 Mbit/s för dataflödet.

## <a name="configuring-a-vpn-gateway"></a>Konfigurera en VPN Gateway

En anslutning för VPN-gateway är beroende av flera resurser som är konfigurerade med specifika inställningar. De flesta av de här resurserna kan konfigureras separat, men i vissa fall måste de konfigureras i en viss ordning.

### <a name="settings"></a>Inställningar

De inställningar som du väljer för varje resurs är viktiga för att skapa en lyckad anslutning.

Information om enskilda resurser och inställningar för en VPN-gateway finns i [om VPN gateway-inställningar för Azure Stack](azure-stack-vpn-gateway-settings.md). Den här artikeln hjälper dig att förstå:

* Gatewaytyper, VPN-typer och anslutningstyper.
* Gateway-undernät, lokala nätverksgatewayer och andra resursinställningar som du kan använda.

### <a name="deployment-tools"></a>Distributionsverktyg

Du kan skapa och konfigurera resurser med hjälp av ett konfigurationsverktyg, till exempel Azure portal. Senare kan du växla till ett annat verktyg som PowerShell för att konfigurera ytterligare resurser eller ändra befintliga resurser när så är tillämpligt. För närvarande går det inte att konfigurera alla resurser och resursinställningar på Azure Portal. Anvisningarna i artiklarna för varje anslutningstopologi anger om ett specifikt konfigurationsverktyg behövs.

## <a name="connection-topology-diagrams"></a>Anslutningstopologi

Det är viktigt att känna till att det finns olika konfigurationer för VPN-gatewayanslutningar. Bestämma vilken konfiguration som passar bäst för dina behov. I följande avsnitt kan du visa information och Topologidiagram om följande VPN-gatewayanslutningar:

* tillgänglig distributionsmodell
* tillgängliga konfigurationsverktyg
* länkar som tar dig direkt till en artikel om en sådan finns

Diagrammen och beskrivningarna i följande avsnitt kan hjälpa dig att välja en anslutningstopologi som passar dina behov. Diagrammen visar de viktigaste grundläggande topologierna, men det är möjligt att skapa mer komplexa konfigurationer med diagrammen som vägledning.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Plats-till-plats och flera platser (IPsec/IKE VPN-tunnel)

### <a name="site-to-site"></a>Plats-till-plats

En plats-till-plats-anslutning (S2S) för VPN-gateway är en anslutning via en VPN-tunnel med IPsec/IKE (IKEv1 eller IKEv2). Den här typen av anslutning kräver en VPN-enhet som du har dem på plats och har tilldelats en offentlig IP-adress. Den här enheten får inte finnas bakom en NAT. S2S-anslutningar kan användas för konfigurationer mellan platser och för hybridkonfigurationer.

![Konfigurationsexempel för plats-till-plats VPN-anslutning](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>Flera platser

Den här typen av anslutning är en variant av plats-till-plats-anslutningen. Du skapar fler än en VPN-anslutning från din virtuella nätverksgateway, vanligtvis sker anslutningen till flera lokala platser. När du arbetar med flera anslutningar måste använda du en RouteBased VPN-typ (kallas en dynamisk gateway när du arbetar med klassiska virtuella nätverk.) Eftersom varje virtuellt nätverk bara kan ha en VPN-gateway delar alla anslutningar via gatewayen på den tillgängliga bandbredden. Det här kallas ofta en ”anslutning till flera platser”.

![Exempel på Azure VPN Gateway-anslutningar för flera platser](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>Gateway-SKU:er

När du skapar en virtuell nätverksgateway för Azure Stack kan ange du vilken gateway-SKU som du vill använda. I följande VPN-gateway SKU: er stöds:

* Basic
* Standard
* HighPerformance

När du väljer en högre gateway-SKU, som Standard än på Basic- eller HighPerformance över Standard eller Basic allokeras fler processorer och mer nätverksbandbredd till gatewayen. Gatewayen kan därför hantera större nätverksgenomflöde till det virtuella nätverket.

Azure Stack har inte stöd för UltraPerformance gateway-SKU som uteslutande används tillsammans med Express Route.

Tänk på följande när du väljer SKU:

* Azure Stack har inte stöd för principbaserade gateways.
* Border Gateway Protocol (BGP) stöds inte på en grundläggande SKU.
* ExpressRoute VPN Gateway samexisterande konfigurationer stöds inte i Azure Stack.
* S2S VPN Gateway-anslutningar av typen aktiv-aktiv kan enbart konfigureras på HighPerformance SKU.

## <a name="estimated-aggregate-throughput-by-sku"></a>Beräknat aggregerat dataflöde av SKU

Följande tabell visar gateway-typerna och beräknat aggregerat dataflöde efter gateway-SKU.

|   | VPN-Gateway, genomflöde *(1)* | VPN-Gateway, Max. IPsec-tunnlar *(2)* |
|-------|-------|-------|
|**Grundläggande SKU** ***(3)***    | 100 Mbit/s  | 10    |
|**Standard-SKU**       | 100 Mbit/s  | 10    |
|**Högpresterande SKU** | 200 Mbit/s    | 5 |

**Tabellen kommentarer:**

*Obs! (1)* -VPN-dataflöde är inte garanterad genomströmning för anslutningar mellan platser över Internet. Det är det högsta möjliga genomflöde måttet.  
*Obs! (2)* -Max tunnlar är det totala antalet per Azure Stack-distribution för alla prenumerationer.  
*Obs! (3)* -BGP-routning stöds inte för en grundläggande SKU.

>[!NOTE]
>Du kan skapa bara en plats-till-plats VPN-anslutning mellan två Azure Stack-distributioner.  Detta beror på en begränsning i den plattform som tillåter bara en enda VPN-anslutning till samma IP-adress.  Eftersom Azure Stack utnyttjar gateway för flera innehavare som använder en enda offentlig IP-adress för alla VPN-gatewayer i Azure Stack-system, kan det finnas bara en VPN-anslutning mellan två Azure Stack-system.  Den här begränsningen gäller även för att ansluta fler än en plats-till-plats-VPN-anslutningar till VPN-Gateway som använder en IP-adress.  Azure Stack tillåter inte fler än en lokal nätverksgateway-resurs som ska skapas med samma IP-adress.


## <a name="next-steps"></a>Nästa steg

[Konfigurationsinställningar för VPN-gateway för Azure Stack](azure-stack-vpn-gateway-settings.md)
