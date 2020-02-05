---
title: Azure VMware-lösningar (AVS)-nätverks check lista
description: Check lista för att allokera nätverks-CIDR på Azure VMware-lösning efter AVS
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5cbb0fc0514c17fe34f8e57806e6620cfa8b3f68
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025018"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-avs"></a>Nätverks krav för Azure VMware-lösning enligt AVS

Azure VMware-lösningar (AVS) erbjuder en privat VMware-moln miljö som är tillgänglig för användare och program från lokala miljöer, företags hanterade enheter och Azure-resurser. Anslutningen levereras via nätverks tjänster som VPN-och Azure ExpressRoute-anslutningar. Några av dessa nätverks tjänster kräver att du anger nätverks adress intervall för att aktivera tjänsterna. 

Tabellerna i den här artikeln beskriver en uppsättning adress intervall och motsvarande tjänster som använder de angivna adresserna. Några av adresserna är obligatoriska och vissa är beroende av de tjänster som du vill distribuera. Dessa adress utrymmen ska inte överlappa något av dina lokala undernät, Azure Virtual Network-undernät eller planerade arbets belastnings under nät.

## <a name="network-address-ranges-required-for-creating-an-avs-private-cloud"></a>Nätverks adress intervall som krävs för att skapa ett privat AVS-moln

När du skapar en AVS-tjänst och ett moln i molnet, måste du uppfylla de angivna CIDR-intervallen för nätverks klasser i samma domän som nedan.

| Namn/Använd för     | Beskrivning                                                                                                                            | Adressintervall            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Gateway-CIDR      | Krävs för Edge Services (VPN-gatewayer). Denna CIDR krävs när AVS-tjänsten skapas och måste vara från RFC 1918-utrymmet. | /28                      |
| vSphere/virtuellt San CIDR | Krävs för VMware Management Networks. Den här CIDR-filen måste anges när det privata molnet skapas.                                    | /24 eller/23 eller/22 eller/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>Nätverks adress intervall som krävs för Azure-nätverksanslutning till ett lokalt nätverk

Anslutning från ett [lokalt nätverk till molnets privata moln nätverk via ExpressRoute](on-premises-connection.md) upprättar en global Reach anslutning. Anslutningen använder Border Gateway Protocol (BGP) för att utväxla vägar mellan ditt lokala nätverk, ditt moln nätverk i molnet och dina Azure-nätverk.

| Namn/Använd för             | Beskrivning                                                                                                                                                                             | Adressintervall |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute peering CIDR | Krävs när du använder ExpressRoute Global Reach för lokal anslutning. Denna CIDR måste anges när en Global Reach anslutningsbegäran görs via ett support ärende. | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>Nätverks adress intervall som krävs för att använda en plats-till-plats-VPN-anslutning till ett lokalt nätverk

Anslutning från ett [lokalt nätverk till molnets privata moln nätverk genom att använda plats-till-plats-VPN](vpn-gateway.md) kräver följande IP-adresser, lokalt nätverk och identifierare. 

| Adress/adress intervall | Beskrivning                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Peer-IP               | Lokal VPN gateway offentlig IP-adress. Krävs för att upprätta en VPN-anslutning från plats till plats mellan ett lokalt Data Center och AVS-tjänsteregion. Den här IP-adressen krävs vid skapande av VPN gateway för plats-till-plats.                                         |
| Peer-ID       | Peer-ID för den lokala VPN-gatewayen. Detta är vanligt vis samma som **peer-IP**.  Om en unik identifierare anges på din lokala VPN-gateway måste identifieraren anges.  Peer-ID krävs vid skapande av VPN gateway för plats-till-plats.   |
| Lokala nätverk   | Lokala prefix som behöver åtkomst till AVS-nätverk i regionen.  Ta med alla prefix från ett lokalt nätverk som kommer att ha åtkomst till AVS-nätverket, inklusive klient nätverket där användare kommer åt nätverket.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Nätverks adress intervall som krävs för att använda punkt-till-plats-VPN-anslutningar

En punkt-till-plats-VPN-anslutning ger åtkomst till AVS-nätverket från en klient dator. [Om du vill konfigurera punkt-till-plats-VPN](vpn-gateway.md)måste du ange följande nätverks adress intervall.

| Adress/adress intervall | Beskrivning                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Klient under nät         | DHCP-adresser tillhandahålls av klient under nätet när du ansluter med hjälp av en punkt-till-plats-VPN. Det här under nätet krävs när du skapar en punkt-till-plats-VPN-gateway på en AVS-Portal. Nätverket är uppdelat i två undernät, ett för UDP-anslutningen och det andra för TCP-anslutningar. |

## <a name="next-steps"></a>Nästa steg

* [Installation av lokala brand väggar för att få åtkomst till ditt AVS-privata moln](on-premises-firewall-configuration.md)
* [Snabb start – skapa en AVS-tjänst](quickstart-create-cloudsimple-service.md)
* [Snabb start – konfigurera ett privat AVS-moln](quickstart-create-private-cloud.md)
* Läs mer om [Azures nätverks anslutningar](cloudsimple-azure-network-connection.md)
* Läs mer om [VPN-gatewayer](cloudsimple-vpn-gateways.md)
