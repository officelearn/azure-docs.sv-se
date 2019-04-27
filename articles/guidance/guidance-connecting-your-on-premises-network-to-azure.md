---
title: Ansluta ditt lokala nätverk till Azure | Microsoft Docs
description: Förklarar och jämför olika metoder för att ansluta till Microsofts molntjänster som Azure, Office 365 och Dynamics CRM Online.
services: ''
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: ''
ms.assetid: 294d39ad-40e0-476a-a362-57911b1172b7
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: cherylmc
ms.openlocfilehash: 9361c0b2ee84d7459bd432f5a8ce13fc5fd85ef8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583544"
---
# <a name="connecting-your-on-premises-network-to-azure"></a>Ansluta ditt lokala nätverk till Azure
Microsoft tillhandahåller flera typer av molntjänster. Du kan ansluta till alla tjänster via det offentliga Internet, kan du också ansluta till de tjänster som använder ett virtuellt privat nätverk (VPN)-tunnel via Internet eller via en direkt, privat anslutning till Microsoft. Den här artikeln hjälper dig att avgöra vilka anslutningsalternativet kommer efter behov baserat på typerna av Microsofts molntjänster som du förbrukar. De flesta organisationer använder flera anslutningstyper som beskrivs nedan.

## <a name="connecting-over-the-public-internet"></a>Ansluta via det offentliga Internet
Den här anslutningstypen ger åtkomst till Microsofts molntjänster direkt via Internet, som visas nedan.

![Internet](./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

Den här anslutningen är vanligtvis den första typen används för att ansluta till Microsofts molntjänster. Tabellen nedan visar en lista över- och nackdelar med den här anslutningstypen.

| **Fördelar** | **Överväganden** |
| --- | --- |
| Kräver inga ändringar till ditt lokala nätverk så länge alla klientenheter har obegränsad åtkomst till alla IP-adresser och portar på Internet. |Även om trafiken krypteras ofta med hjälp av HTTPS, kan det fångas upp under överföring eftersom den passerar det offentliga Internet. |
| Kan ansluta till alla Microsoft-molntjänster som är exponerade för det offentliga Internet. |Oförutsägbara svarstid eftersom anslutningen går via Internet. |
| Använder din befintliga Internetanslutning. | |
| Kräver inte hantering av enheter för någon anslutning. | |

Den här anslutningen har ingen anslutning eller kostnader för bandbredd, eftersom du använder din befintliga Internetanslutning.

## <a name="connecting-with-a-point-to-site-connection"></a>Ansluta med en punkt-till-plats-anslutning
Den här anslutningstypen ger åtkomst till vissa Microsofts molntjänster via en Secure Socket Tunneling Protocol (SSTP)-tunnel via Internet, som visas nedan.

![P2S](./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "punkt-till-plats-anslutning")

Anslutningen görs över din befintliga Internetanslutning, men kräver användning av Azure VPN Gateway. Tabellen nedan visar en lista över- och nackdelar med den här anslutningstypen.

| **Fördelar** | **Överväganden** |
| --- | --- |
| Kräver inga ändringar till ditt lokala nätverk så länge alla klientenheter har obegränsad åtkomst till alla IP-adresser och portar på Internet. |Om trafik är krypterad med hjälp av IPSec, kan det fångas upp under överföring eftersom den passerar det offentliga Internet. |
| Använder din befintliga Internetanslutning. |Oförutsägbara svarstid eftersom anslutningen går via Internet. |
| Dataflöde upp till 200 Mb/s per gateway. |Kräver skapandet och hanteringen av separata anslutningar mellan varje enhet i ditt lokala nätverk och varje gateway som varje enhet behöver ansluta till. |
| Kan användas för att ansluta till Azure-tjänster som kan anslutas till en Azure-nätverk (VNet), till exempel virtuella datorer i Azure och Azure Cloud Services. |Kräver minimal pågående administration av Azure VPN Gateway. |
| Kan inte användas för att ansluta till Microsoft Office 365 eller Dynamics CRM Online. | |
| Kan inte användas för att ansluta till Azure-tjänster som inte kan anslutas till ett virtuellt nätverk. | |

Läs mer om den [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) tjänsten, dess [priser](https://azure.microsoft.com/pricing/details/vpn-gateway), och utgående dataöverföring [priser](https://azure.microsoft.com/pricing/details/data-transfers).

## <a name="connecting-with-a-site-to-site-connection"></a>Ansluta till en plats-till-plats-anslutning
Den här anslutningstypen ger åtkomst till vissa Microsofts molntjänster via en IPSec-tunnel via Internet, som visas nedan.

![S2S](./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "plats-till-plats-anslutning")

Anslutningen görs över din befintliga Internetanslutning, men kräver användning av Azure VPN Gateway med dess associerade priser och prissättningen för utgående dataöverföring. Tabellen nedan visar en lista över- och nackdelar med den här anslutningstypen.

| **Fördelar** | **Överväganden** |
| --- | --- |
| Alla enheter i ditt lokala nätverk kan kommunicera med Azure-tjänster som är anslutna till ett virtuellt nätverk så du behöver inte konfigurera enskilda anslutningar för varje enhet. |Om trafik är krypterad med hjälp av IPSec, kan det fångas upp under överföring eftersom den passerar det offentliga Internet. |
| Använder din befintliga Internetanslutning. |Oförutsägbara svarstid eftersom anslutningen går via Internet. |
| Kan användas för att ansluta till Azure-tjänster som kan anslutas till ett virtuellt nätverk till exempel virtuella datorer och molntjänster. |Konfigurera och hantera en validerade VPN-enheten * lokalt. |
| Dataflöde upp till 200 Mb/s per gateway. |Kräver minimal pågående administration av Azure VPN Gateway. |
| Kan tvinga utgående trafik som initieras från molnbaserade virtuella datorer via det lokala nätverket för granskning och loggning med användardefinierade vägar eller Border Gateway Protocol (BGP) **. |Kan inte användas för att ansluta till Microsoft Office 365 eller Dynamics CRM Online. |
| Kan inte användas för att ansluta till Azure-tjänster som inte kan anslutas till ett virtuellt nätverk. | |
| Om du använder tjänster som initierar anslutningar till lokala enheter och IPSec-principer kräver detta, kanske du måste en brandvägg mellan det lokala nätverket och Azure. | |

* * Visa en lista över [validerade VPN-enheter](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable).
* ** Läs mer om hur du använder [användardefinierade vägar](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) eller [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) att tvinga routning från virtuella Azure-nätverk till en lokal enhet.

## <a name="connecting-with-a-dedicated-private-connection"></a>Ansluta med en dedikerad privat anslutning
Den här anslutningstypen ger åtkomst till alla Microsofts molntjänster via en dedikerad privat anslutning till Microsoft som inte sker via Internet, som visas nedan.

![ER](./media/guidance-connecting-your-on-premises-network-to-azure/er.png "ExpressRoute-anslutning")

Anslutningen kräver användning av ExpressRoute-tjänsten och en anslutning till en anslutningsleverantör. Tabellen nedan visar en lista över- och nackdelar med den här anslutningstypen.

| **Fördelar** | **Överväganden** |
| --- | --- |
| Trafik inte kan fångas upp överförs via det offentliga Internet eftersom en dedikerad anslutning via en tjänstleverantör används. |Kräver lokal router-hantering. |
| Bandbredd upp till 10 Gb/s per ExpressRoute-krets och dataflöde upp till 2 Gb/s till varje gateway. |Kräver en dedikerad anslutning till en anslutningsleverantör. |
| Förutsägbar svarstid eftersom det är en dedikerad anslutning till Microsoft som inte sker via Internet. |Kan kräva minimal pågående administration av en eller flera Azure VPN gateway (om du ansluter kretsen till virtuella nätverk). |
| Kräver inte krypterad kommunikation, även om du kan kryptera trafiken, om så önskas. |Om du använder molntjänster som initierar anslutningar till lokala enheter kan behöva du en brandvägg mellan det lokala nätverket och Azure. |
| Ansluta direkt till alla Microsoft-molntjänster, med några undantag *. |Kräver network adress translation (NAT) med den lokala IP-adresser att ange Microsoft-datacenter för tjänster som inte kan anslutas till VNet.* * |
| Tvinga utgående trafik som initieras från molnbaserade virtuella datorer via det lokala nätverket för granskning och loggning med hjälp av BGP. | |

* * Visa en [listan över tjänster](../expressroute/expressroute-faqs.md#supported-services) som inte kan användas med ExpressRoute. Din Azure-prenumeration måste godkännas för att ansluta till Office 365.  Se den [Azure ExpressRoute för Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) nedan för information.
* ** Läs mer om ExpressRoute [NAT](../expressroute/expressroute-nat.md) krav.

Läs mer om [ExpressRoute](../expressroute/expressroute-introduction.md), den är kopplad till [priser](https://azure.microsoft.com/pricing/details/expressroute), och [anslutningsleverantörer](../expressroute/expressroute-locations.md#locations).

## <a name="additional-considerations"></a>Annat som är bra att tänka på
* Flera av alternativen ovan har olika gränsvärden som de har stöd för VNet-anslutningar, gateway-anslutningar och andra kriterier. Vi rekommenderar att du läser igenom Azure [nätverksgränser](../azure-subscription-service-limits.md#networking-limits) att förstå om någon av dem påverkar de typerna av anslutning som du vill använda.
* Om du planerar att ansluta en gateway från en plats-till-plats VPN-anslutning till samma VNet som en ExpressRoute-gateway bör du bekanta dig med viktiga begränsningar först. Se den [konfigurera ExpressRoute och plats-till-plats-anslutningar för samexistens mellan](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) nedan för mer information.

## <a name="next-steps"></a>Nästa steg
Resurserna nedan beskriver hur du implementerar anslutningstyper som beskrivs i den här artikeln.

* [Implementera en punkt-till-plats-anslutning](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Implementera en plats-till-plats-anslutning](guidance-hybrid-network-vpn.md)
* [Implementera en dedikerad privat anslutning](guidance-hybrid-network-expressroute.md)
* [Implementera en dedikerad privat anslutning med en plats-till-plats-anslutning för hög tillgänglighet](guidance-hybrid-network-expressroute-vpn-failover.md)
