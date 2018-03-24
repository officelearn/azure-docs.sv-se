---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d562e58b86a6d5c18b10683dc4c14cf9c28c809b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
Vanliga frågor och svar om VNet-till-VNet gäller för VPN-Gateway-anslutningar. Om du letar efter VNet-Peering, se [Peering för virtuella nätverk](../articles/virtual-network/virtual-network-peering-overview.md)

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Tar Azure ut avgifter för trafik mellan virtuella nätverk?

Trafik mellan virtuella nätverk inom samma region är kostnadsfri i båda riktningarna när du använder en VPN-gatewayanslutning. Utgående trafik mellan virtuella nätverk i flera regioner debiteras med priset på utgående dataöverföring mellan virtuella nätverk, baserat på källregionerna. Se [sidan med prissättning för VPN-gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/) för mer information. Om du ansluter dina virtuella nätverk med VNet-peering istället för med VPN-gateway kan du titta på [sidan med prissättning för Virtual Network](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>Skickas trafik mellan virtuella nätverk via Internet?

Nej. Trafik mellan virtuella nätverk skickas via Microsoft Azure-stamnätet, och inte via Internet.

### <a name="is-vnet-to-vnet-traffic-secure"></a>Är trafiken mellan virtuella nätverk säker?

Ja, den är skyddad med IPsec/IKE-kryptering.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Behöver jag en VPN-enhet för att koppla ihop virtuella nätverk?

Nej. Att ansluta flera virtuella Azure-nätverk till varandra kräver inte någon VPN-enhet, såvida inte en anslutning mellan flera platser krävs.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>Behöver mina virtuella nätverk finnas inom samma region?

Nej. De virtuella nätverken kan finnas i samma eller olika Azure-regioner (platser).

### <a name="if-the-vnets-are-not-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-ad-tenant"></a>Om de virtuella nätverken inte är i samma prenumeration, måste prenumerationerna associeras med samma AD-klient?

Nej.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Kan jag använda VNet-till-VNet för att ansluta virtuella nätverk i separata Azure-instanser? 

Nej. Du kan använda VNet-till-VNet för att ansluta virtuella nätverk inom samma Azure-instans. Du kan till exempel inte skapa en anslutning mellan offentliga Azure och Azure-instanserna för Kina, Tyskland eller US Gov. I dessa scenarier kan du överväga att använda en VPN-anslutning för plats-till-plats.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>Kan jag använda anslutningar mellan virtuella nätverk tillsammans med anslutningar mellan flera platser?

Ja. Virtuell nätverksanslutning kan användas samtidigt med VPN på flera platser.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Hur många lokala platser och virtuella nätverk kan ett virtuellt nätverk ansluta till?

Se tabellen med [Gateway-krav](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements).

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>Kan jag använda anslutningar mellan virtuella nätverk för att ansluta virtuella datorer eller molntjänster utanför en virtuellt nätverk?

Nej. VNet-till-VNet stöder anslutning av virtuella nätverk. Det stöder inte anslutning av virtuella datorer eller molntjänster som inte finns i ett virtuellt nätverk.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Kan en molntjänst eller en slutpunkt för utjämning av nätverksbelastning sträcka sig över virtuella nätverk?

Nej. En molntjänst eller en slutpunkt för belastningsutjämning kan inte omfatta flera virtuella nätverk, även om de är sammankopplade.

### <a name="can-i-used-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Kan jag använda en principbaserad VPN-typ för virtuellt nätverk till virtuellt nätverk eller flerplatsanslutningar?

Nej. Anslutningar mellan olika virtuella nätverk och flera platser kräver Azure VPN-gatewayer med VPN-typen RouteBased (tidigare kallad dynamisk routning).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Kan jag ansluta ett VNet med en routningsbaserad VPN-typ till ett annat VNet med en policybaserad VPN-typ?

Nej, båda virtuella nätverken MÅSTE använda routningsbaserade VPN-anslutningar (tidigare kallat dynamisk routning).

### <a name="do-vpn-tunnels-share-bandwidth"></a>Delar VPN-tunnlar bandbredd?

Ja. Alla VPN-tunnlar i det virtuella nätverket delar på den tillgängliga bandbredden i Azures VPN-gateway och samma VPN-gateways upptids-SLA i Azure.

### <a name="are-redundant-tunnels-supported"></a>Finns det stöd för redundanta tunnlar?

Redundanta tunnlar mellan ett par med virtuella nätverk stöds när en virtuell nätverksgateway är konfigurerad som aktiv-aktiv.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Kan jag har överlappande adressutrymmen för konfigurationer mellan virtuella nätverk?

Nej. Du kan inte ha överlappande IP-adressintervall.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Får det finnas överlappande adressutrymmen i anslutna virtuella nätverk och på lokala platser?

Nej. Du kan inte ha överlappande IP-adressintervall.



