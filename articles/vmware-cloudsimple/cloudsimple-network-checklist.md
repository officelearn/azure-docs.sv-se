---
title: Azure VMware-lösning från CloudSimple – checklista för nätverk
description: Checklista för allokering av nätverks-CIDR på Azure VMware-lösning per CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025018"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Nätverksförutsättning för Azure VMware Solution by CloudSimple

Azure VMware Solution by CloudSimple erbjuder en VMware-privat molnmiljö som är tillgänglig för användare och program från lokala miljöer, företagshanterade enheter och Azure-resurser. Anslutningen levereras via nätverkstjänster som VPN och Azure ExpressRoute-anslutningar. Vissa av dessa nätverkstjänster kräver att du anger nätverksadressintervall för att aktivera tjänsterna. 

Tabeller i den här artikeln beskriver uppsättningen adressintervall och motsvarande tjänster som använder de angivna adresserna. Vissa adresser är obligatoriska och vissa är beroende av vilka tjänster du vill distribuera. Dessa adressutrymmen bör inte överlappa med något av dina lokala undernät, Undernät för Virtuella Azure-nätverk eller planerade CloudSimple-arbetsbelastningsundernät.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Nätverksadressintervall som krävs för att skapa ett privat moln

När du skapar en CloudSimple-tjänst och ett privat moln måste du följa de angivna cidr-intervallen (Network Classless Inter-Domain Routing), enligt följande.

| Namn/används för     | Beskrivning                                                                                                                            | Adressintervall            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Gateway CIDR      | Krävs för kanttjänster (VPN-gateways).  Den här CIDR krävs under skapandet av CloudSimple-tjänsten och måste komma från RFC 1918-utrymmet. | /28                      |
| vSphere/vSAN CIDR | Krävs för VMware-hanteringsnätverk. Den här CIDR-regeln måste anges när det privata molnet skapas.                                    | /24 eller /23 eller /22 eller /21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Nätverksadressintervall som krävs för Azure-nätverksanslutning till ett lokalt nätverk

Genom att ansluta från ett [lokalt nätverk till det privata molnnätverket via ExpressRoute](on-premises-connection.md) upprättas en Global Reach-anslutning.  Anslutningen använder BGP (Border Gateway Protocol) för att utbyta vägar mellan ditt lokala nätverk, ditt privata molnnätverk och dina Azure-nätverk.

| Namn/används för             | Beskrivning                                                                                                                                                                             | Adressintervall |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute Peering CIDR | Krävs när du använder ExpressRoute Global Reach för lokal anslutning. Denna CIDR måste tillhandahållas när en begäran om global räckviddsanslutning görs via en supportbiljett. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>Nätverksadressintervall som krävs för att använda en plats-till-plats-VPN-anslutning till ett lokalt nätverk

För att ansluta från ett [lokalt nätverk till det privata molnnätverket med hjälp av plats-till-plats-VPN](vpn-gateway.md) krävs följande IP-adresser, lokala nätverk och identifierare. 

| Adress-/adressintervall | Beskrivning                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Peer-IP               | Lokal VPN-gateway offentlig IP-adress. Krävs för att upprätta en plats-till-plats-VPN-anslutning mellan ett lokalt datacenter och CloudSimple-tjänsten. Den här IP-adressen krävs när VPN-gateway skapas från plats till plats.                                         |
| Peer-identifierare       | Peer-identifierare för den lokala VPN-gatewayen. Detta är vanligtvis samma som **peer IP**.  Om en unik identifierare anges på din lokala VPN-gateway måste identifieraren anges.  Peer-ID krävs när VPN-gateway skapas från plats till plats.   |
| Lokala nätverk   | Lokala prefix som behöver åtkomst till CloudSimple-nätverk i regionen.  Inkludera alla prefix från ett lokalt nätverk som kommer åt CloudSimple-nätverket, inklusive klientnätverket där användarna kommer åt nätverket.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Nätverksadressintervall som krävs för att använda punkt-till-plats-VPN-anslutningar

En punkt-till-plats VPN-anslutning ger åtkomst till CloudSimple-nätverket från en klientdator.  [Om du vill konfigurera punkt-till-plats-VPN](vpn-gateway.md)måste du ange följande nätverksadressintervall.

| Adress-/adressintervall | Beskrivning                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Undernät för klient         | DHCP-adresser tillhandahålls av klientundernätet när du ansluter med hjälp av en punkt-till-plats-VPN. Det här undernätet krävs när du skapar en vpn-gateway på en CloudSimple-portal.  Nätverket är uppdelat i två undernät. en för UDP-anslutningen och den andra för TCP-anslutningar. |

## <a name="next-steps"></a>Nästa steg

* [Lokal brandväggskonfiguration för åtkomst till ditt privata moln](on-premises-firewall-configuration.md)
* [Snabbstart - Skapa en CloudSimple-tjänst](quickstart-create-cloudsimple-service.md)
* [Snabbstart- Konfigurera ett privat moln](quickstart-create-private-cloud.md)
* Läs mer om [Azure-nätverksanslutningar](cloudsimple-azure-network-connection.md)
* Läs mer om [VPN-gateways](cloudsimple-vpn-gateways.md)
