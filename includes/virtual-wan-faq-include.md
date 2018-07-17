---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/10/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8c3f727c6154a0364f151d22000d2684c361676a
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39037218"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Vad är skillnaden mellan en virtuell nätverksgateway i Azure (VPN Gateway) och en Azure Virtual WAN-vpngateway?

Virtual WAN tillhandahåller storskalig plats-till-plats-anslutning och byggs för dataflöde, skalbarhet och användarvänlighet. Automatiskt tillhandahållande av CPE-grenenheter och anslutning till Azure Virtual WAN. Enheterna finns tillgängliga från ett växande ekosystem av SD-WAN- och VPN-partner.

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>Vilken enhetsprovider (Virtual WAN-partner) stöds vid start? 

Just nu stöds den helt automatiserade Virtual WAN-upplevelsen i Citrix och Riverbed. Fler partner ansluter sig de kommande månaderna, bland annat: Nokia Nuage, Palo Alto och Checkpoint. Mer information finns i [Virtual WAN-partner](https://aka.ms/virtualwan).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Måste jag använda en önskad partnerenhet?

Nej. Du kan använda valfri VPN-kompatibel enhet som följer förhandsversionskraven för IKEv2 IPsec-support.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Hur automatiserar Virtual WAN-partner anslutningsmöjligheter med Azure Virtual WAN?

Programvarudefinierade anslutningslösningar hanterar vanligtvis gren-enheter med hjälp av en kontrollant eller ett enhetsetableringscenter. Kontrollanten kan automatisera anslutningar till Azure Virtual WAN med hjälp av Azure API:er. Mer information om hur detta fungerar finns i [Automation av Virtual WAN-partner](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>Ändrar Virtual WAN några befintliga anslutningsfunktioner?   

Inga befintliga anslutningsfunktioner i Azure ändras.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Finns nya Resource Manager-resurser tillgängliga för Virtual WAN?
  
Ja, Virtual WAN introducerar ny Resource Manager-resurser. Mer information finns i [Översikt](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="are-there-any-special-requirements-to-join-the-preview"></a>Finns det några särskilda krav för att ta del av förhandsversionen? 

Du måste registrera prenumerationen i förhandsversionen innan du kan konfigurera ett Azure Virtual WAN. Skicka ett e-postmeddelande till <azurevirtualwan@microsoft.com> med ditt prenumerations-ID om du vill registrera dig. Du får ett e-postmeddelande tillbaka när din prenumeration har registrerats. Tills du får bekräftelse att prenumerationen har registrerats kan du inte arbeta med Virtual WAN i portalen.

Överväganden:

* Förhandsversionen är begränsad till de offentliga Azure-regionerna.
* Upp till 100 anslutningar stöds per virtuell hubb. Varje anslutning består av två tunnlar i konfigurationen aktiv-aktiv. Tunnlarna avslutas i en Azure Virtual Hub-vpngateway.
* Överväg att använda den här förhandsversionen om:
  * Du vill distribuera samlad bandbredd på mindre än 1 Gbit/s per virtuell hubb.
  * Du har en VPN-enhet som stöder routningsbaserad konfiguration och IKEv2 IPsec-anslutningar.
  * Du vill prova att använda SD-WAN och operativa grenenheter från startpartner (Riverbed och Citrix).<br>eller<br>Du vill konfigurera gren-till-gren- och gren-till-Azure-anslutningar som inkluderar konfigurationshantering av de lokala enheterna. (Konfigurera själv)

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>Stöds Global VNet-peering med Azure Virtual WAN? 

 Nej.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Kan ekernätverk som är anslutna till en virtuell hubb kommunicera med varandra?

Ja. Du kan utföra VNet-peering direkt mellan ekrar som är ansluta till en virtuell hubb. Mer information finns i [Virtual Network-peering](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Kan jag distribuera och använda virtuell installation av mitt favoritnätverk (i ett virtuellt NVA-nätverk) med Azure Virtual WAN?

Ja, du kan ansluta till virtuell installation av ditt virtuella favoritnätverk (NVA) till Azure Virtual WAN, men du behöver routningsfunktioner i hubben som finns på GA. Alla ekrar som är anslutna till NVA MNet måste dessutom anslutas till den virtuella hubben. 

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Kan ett NVA-nätverk ha en virtuell nätverksgateway?

Nej. Det virtuella NVA-nätverket kan inte ha en virtuell nätverksgateway om den är ansluten till den virtuella hubben. 

### <a name="is-there-support-for-bgp"></a>Stöds BGP?

Ja, BGP stöds. För att säkerställa att routningar från ett virtuellt NVA-nätverk annonseras på ett lämpligt sätt måste ekrar inaktivera BGP om de är anslutna till ett virtuellt NVA-nätverk, som i sin tur är anslutet till en virtuell hubb. Anslut dessutom ekernätverken till den virtuella hubben.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Kan jag dirigera trafik med hjälp av UDR i den virtuella hubben?

UDR och routningsfunktioner finns tillgängliga via GA.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Finns det licens- eller prisinformation om Virtual WAN?
 
Det tillkommer inga kostnader under förhandsversionen. Aktuella [Azure VPN- och utgående priser](https://azure.microsoft.com/pricing/details/vpn-gateway/) gäller under förhandsversionen.