---
title: "Ansluter dina lokala nätverk till Azure | Microsoft Docs"
description: "Beskriver och jämför olika metoder för att ansluta till Microsoft-molntjänster som Azure, Office 365 och Dynamics CRM Online."
services: 
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: 
ms.assetid: 294d39ad-40e0-476a-a362-57911b1172b7
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: cherylmc
ms.openlocfilehash: 37d83d3b6dea1763d85f2411816ba2fee4279100
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="connecting-your-on-premises-network-to-azure"></a>Ansluter dina lokala nätverk till Azure
Microsoft tillhandahåller flera olika typer av molntjänster. Medan du kan ansluta till alla tjänster via det offentliga Internet, kan du också ansluta till de tjänster som använder en tunnel för virtuellt privat nätverk (VPN) via Internet eller via en direkt, privat anslutning till Microsoft. Den här artikeln hjälper dig att avgöra vilket alternativ för anslutningen som passar dina behov baserat på typerna av Microsofts molntjänster som du använder. De flesta organisationer använder flera anslutningar som beskrivs nedan.

## <a name="connecting-over-the-public-internet"></a>Ansluter via det offentliga Internet
Den här anslutningstypen ger åtkomst till Microsofts molntjänster direkt via Internet, som visas nedan.

![Internet](./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

Den här anslutningen är vanligtvis den första typen används för att ansluta till Microsofts molntjänster. I tabellen nedan visas- och nackdelar med den här anslutningstypen.

| **Förmåner** | **Överväganden** |
| --- | --- |
| Kräver inga ändringar till det lokala nätverket så länge alla klientenheter har obegränsad åtkomst till alla IP-adresser och portar på Internet. |Även om trafiken krypteras ofta med hjälp av HTTPS, kan den fångas under överföring eftersom den passerar det offentliga Internet. |
| Kan ansluta till alla Microsoft-molntjänster som exponeras för det offentliga Internet. |Oväntade latens eftersom anslutningen går via Internet. |
| Använder befintliga Internet-anslutningen. | |
| Kräver inte hantering av alla enheter som anslutningen. | |

Den här anslutningen har ingen anslutning eller kostnader för bandbredd, eftersom du kan använda din befintliga Internet-anslutning.

## <a name="connecting-with-a-point-to-site-connection"></a>Ansluta med en punkt-till-plats-anslutning
Den här anslutningstypen ger åtkomst till vissa Microsoft-molntjänster genom en tunnel Secure Socket Tunneling Protocol (SSTP) via Internet, som visas nedan.

![P2S](./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "punkt-till-plats-anslutning")

Anslutningen har gjorts via din befintliga Internet-anslutning, men kräver användning av en Azure VPN-Gateway. I tabellen nedan visas- och nackdelar med den här anslutningstypen.

| **Förmåner** | **Överväganden** |
| --- | --- |
| Kräver inga ändringar till det lokala nätverket så länge alla klientenheter har obegränsad åtkomst till alla IP-adresser och portar på Internet. |Även om trafiken krypteras med hjälp av IPSec, kan den fångas under överföring eftersom den passerar det offentliga Internet. |
| Använder befintliga Internet-anslutningen. |Oväntade latens eftersom anslutningen går via Internet. |
| Genomströmning upp till 200 Mb/s per gateway. |Kräver skapande och hantering av separata anslutningar mellan varje enhet i ditt lokala nätverk och varje gateway varje enhet behöver ansluta till. |
| Kan användas för att ansluta till Azure-tjänster som kan vara ansluten till en virtuell Azure-nätverk (VNet), till exempel virtuella datorer i Azure och Azure Cloud Services. |Kräver minimal pågående administration av en Azure VPN-Gateway. |
| Kan inte användas för att ansluta till Microsoft Office 365 eller Dynamics CRM Online. | |
| Kan inte användas för att ansluta till Azure-tjänster som inte kan anslutas till ett virtuellt nätverk. | |

Lär dig mer om den [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) tjänsten, dess [priser](https://azure.microsoft.com/pricing/details/vpn-gateway), utgående data Transfer [priser](https://azure.microsoft.com/pricing/details/data-transfers).

## <a name="connecting-with-a-site-to-site-connection"></a>Ansluta till en plats-till-plats-anslutning
Den här anslutningstypen ger åtkomst till vissa Microsoft-molntjänster genom en IPSec-tunnel via Internet, som visas nedan.

![S2S](./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "plats-till-plats-anslutning")

Anslutningen har gjorts via din befintliga Internet-anslutning, men kräver användning av en Azure VPN-Gateway med associerade priser och utgående dataöverföring priser. I tabellen nedan visas- och nackdelar med den här anslutningstypen.

| **Förmåner** | **Överväganden** |
| --- | --- |
| Alla enheter i det lokala nätverket kan kommunicera med Azure-tjänster ansluts till ett VNet så inte behöver du konfigurera enskilda anslutningar för varje enhet. |Även om trafiken krypteras med hjälp av IPSec, kan den fångas under överföring eftersom den passerar det offentliga Internet. |
| Använder befintliga Internet-anslutningen. |Oväntade latens eftersom anslutningen går via Internet. |
| Kan användas för att ansluta till Azure-tjänster som kan vara ansluten till ett virtuellt nätverk som virtuella datorer och molntjänster. |Konfigurera och hantera en validerade VPN-enhet * lokalt. |
| Genomströmning upp till 200 Mb/s per gateway. |Kräver minimal pågående administration av en Azure VPN-Gateway. |
| Kan tvinga utgående trafik som initieras från molnet virtuella datorer via lokalt nätverk för kontroll och loggning med användardefinierade vägar eller Border Gateway Protocol (BGP) **. |Kan inte användas för att ansluta till Microsoft Office 365 eller Dynamics CRM Online. |
| Kan inte användas för att ansluta till Azure-tjänster som inte kan anslutas till ett virtuellt nätverk. | |
| Om du använder tjänster som initierar anslutningar till lokala enheter och dina säkerhetsprinciper kräver detta, kanske du behöver en brandvägg mellan lokala nätverk och Azure. | |

* * Visa en lista över [verifiera VPN-enheter](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable).
* ** Lär dig mer om hur du använder [användardefinierade vägar](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) eller [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) att tvinga routning från Azure Vnet till en lokal enhet.

## <a name="connecting-with-a-dedicated-private-connection"></a>Ansluta med en dedikerad privat anslutning
Den här anslutningstypen ger åtkomst till alla Microsoft-molntjänster via en dedikerad privata anslutning till Microsoft som inte sker via Internet, som visas nedan.

![ER](./media/guidance-connecting-your-on-premises-network-to-azure/er.png "ExpressRoute-anslutning")

Anslutningen kräver användningen av ExpressRoute-tjänsten och en anslutning till en provider för anslutningen. I tabellen nedan visas- och nackdelar med den här anslutningstypen.

| **Förmåner** | **Överväganden** |
| --- | --- |
| Trafik kan inte fångas upp under överföring via det offentliga Internet eftersom en dedicerad anslutning via en tjänstprovider används. |Kräver lokal hantering av routern. |
| Bandbredd 10 Gbit/s per ExpressRoute-krets och dataflöde 2 Gbit/s till varje gateway. |Kräver en dedicerad anslutning till en provider för anslutningen. |
| Förutsägbar latens eftersom det är en dedicerad anslutning till Microsoft som inte sker via Internet. |Minimal pågående administration av en eller flera Azure VPN-gatewayer (om krävs ansluter kretsen till Vnet). |
| Kräver inte krypterad kommunikation, även om du kan kryptera trafiken, om så önskas. |Om du använder molntjänster som initierar anslutningar till lokala enheter kanske du behöver en brandvägg mellan lokala nätverk och Azure. |
| Kan ansluta direkt till alla Microsoft-molntjänster, med några få undantag *. |Kräver network address translation (NAT) av lokala IP-adresser anger Microsoft-datacenter för tjänster som inte kan anslutas till VNet.* * |
| Tvinga utgående trafik som initieras från molnet virtuella datorer via lokalt nätverk för kontroll och loggning med hjälp av BGP. | |

* * Visa en [lista över tjänster](../expressroute/expressroute-faqs.md#supported-services) som inte kan användas med ExpressRoute. Din Azure-prenumeration måste godkännas för att ansluta till Office 365.  Finns det [Azure ExpressRoute för Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) artikeln för information.
* ** Mer information om ExpressRoute [NAT](../expressroute/expressroute-nat.md) krav.

Lär dig mer om [ExpressRoute](../expressroute/expressroute-introduction.md), dess associerade [priser](https://azure.microsoft.com/pricing/details/expressroute), och [anslutning providers](../expressroute/expressroute-locations.md#locations).

## <a name="additional-considerations"></a>Annat som är bra att tänka på
* Flera av alternativen ovan har olika gränsvärden som de kan ha stöd för VNet-anslutningar, gateway-anslutningar och andra villkor. Vi rekommenderar att du läser igenom Azure [nätverk gränser](../azure-subscription-service-limits.md#networking-limits) att förstå om någon av dem påverkar vilka anslutning du vill använda.
* Om du planerar att ansluta en gateway från en plats-till-plats VPN-anslutning till samma virtuella nätverk som en ExpressRoute-gateway, bör du bekanta dig med viktiga begränsningar först. Finns det [konfigurera ExpressRoute- och plats-till-plats samtidiga anslutningar](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) mer information.

## <a name="next-steps"></a>Nästa steg
Nedan beskrivs hur du implementerar anslutningstyper som beskrivs i den här artikeln.

* [Implementera en punkt-till-plats-anslutning](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Implementera en plats-till-plats-anslutning](guidance-hybrid-network-vpn.md)
* [Implementera en dedikerad privat anslutning](guidance-hybrid-network-expressroute.md)
* [Implementera en dedikerad privat anslutning med en plats-till-plats-anslutning för hög tillgänglighet](guidance-hybrid-network-expressroute-vpn-failover.md)
