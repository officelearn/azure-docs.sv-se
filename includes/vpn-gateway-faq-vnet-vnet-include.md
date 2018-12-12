---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/03/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 72ddd0b6cd6c3e12417d3698c403f89312b531f4
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111631"
---
VNet-till-VNet vanliga frågor och svar gäller för VPN gateway-anslutningar. Information om VNet-peering finns i [peerkoppling av virtuella nätverk](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Tar Azure ut avgifter för trafik mellan virtuella nätverk?

VNet-till-VNet-trafik i samma region är kostnadsfri i båda riktningarna när du använder en VPN-gatewayanslutning. Interregionala VNet-till-VNet utgående trafik debiteras med utgående inter-VNet-dataöverföring baserat på regioner. Mer information finns i [VPN-Gateway sidan med priser](https://azure.microsoft.com/pricing/details/vpn-gateway/). Om du ansluter ditt virtuella nätverk med hjälp av VNet-peering i stället för en VPN-gateway, se [prissättningen för virtuella](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>VNet-till-VNet-trafik färdas över internet?

Nej. VNet-till-VNet-trafik skickas via Microsoft Azure-stamnätet, inte via internet.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-azure-active-directory-aad-tenants"></a>Kan jag upprätta en VNet-till-VNet-anslutning via Azure Active Directory (AAD)-klienter?

Ja, VNet-till-VNet-anslutningar som använder Azure VPN-gateways fungerar mellan AAD-klienter.

### <a name="is-vnet-to-vnet-traffic-secure"></a>Är trafiken mellan virtuella nätverk säker?

Ja, den är skyddad med IPsec/IKE-kryptering.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Behöver jag en VPN-enhet för att koppla ihop virtuella nätverk?

Nej. Att ansluta flera virtuella Azure-nätverk till varandra kräver inte någon VPN-enhet, såvida inte en anslutning mellan flera platser krävs.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>Behöver mina virtuella nätverk finnas inom samma region?

Nej. De virtuella nätverken kan finnas i samma eller olika Azure-regioner (platser).

### <a name="if-the-vnets-arent-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-active-directory-tenant"></a>Om de virtuella nätverken inte är i samma prenumeration, prenumerationerna som behöver vara kopplad till samma Active Directory-klient?

Nej.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Kan jag använda VNet-till-VNet för att ansluta virtuella nätverk i separata Azure-instanser? 

Nej. Du kan använda VNet-till-VNet för att ansluta virtuella nätverk inom samma Azure-instans. Exempel: du kan inte skapa en anslutning mellan globala Azure och kinesiska/tyska/US government Azure instanser. Överväg att använda en plats-till-plats-VPN-anslutning för dessa scenarier.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>Kan jag använda anslutningar mellan virtuella nätverk tillsammans med anslutningar mellan flera platser?

Ja. Virtuell nätverksanslutning kan användas samtidigt med VPN på flera platser.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Hur många lokala platser och virtuella nätverk kan ett virtuellt nätverk ansluta till?

Se den [Gateway-krav](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements) tabell.

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>Kan jag använda anslutningar mellan virtuella nätverk för att ansluta virtuella datorer eller molntjänster utanför en virtuellt nätverk?

Nej. VNet-till-VNet stöder anslutning av virtuella nätverk. Det stöder inte anslutning av virtuella datorer eller molntjänster som inte är i ett virtuellt nätverk.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Kan en molntjänst eller en slutpunkt för Utjämning av nätverksbelastning sträcka sig över virtuella nätverk?

Nej. En molntjänst eller en slutpunkt för belastningsutjämning kan inte omfatta flera virtuella nätverk, även om de är sammankopplade.

### <a name="can-i-use-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Kan jag använda en PolicyBased VPN-typ för VNet-till-VNet eller flerplatsanslutningar?

Nej. VNet-till-VNet och flera platser kräver Azure VPN-gatewayer med RouteBased (tidigare kallad dynamisk routning) VPN-typer.

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Kan jag ansluta ett VNet med en routningsbaserad VPN-typ till ett annat VNet med en policybaserad VPN-typ?

Nej, båda virtuella nätverken måste använda routningsbaserade (tidigare kallat dynamisk routning) VPN: er.

### <a name="do-vpn-tunnels-share-bandwidth"></a>Delar VPN-tunnlar bandbredd?

Ja. Alla VPN-tunnlar i det virtuella nätverket delar på den tillgängliga bandbredden i Azures VPN-gateway och samma VPN-gateways upptids-SLA i Azure.

### <a name="are-redundant-tunnels-supported"></a>Finns det stöd för redundanta tunnlar?

Redundanta tunnlar mellan ett par med virtuella nätverk stöds när en virtuell nätverksgateway är konfigurerad som aktiv-aktiv.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Kan jag har överlappande adressutrymmen för konfigurationer mellan virtuella nätverk?

Nej. Du kan inte ha överlappande IP-adressintervall.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Får det finnas överlappande adressutrymmen i anslutna virtuella nätverk och på lokala platser?

Nej. Du kan inte ha överlappande IP-adressintervall.



