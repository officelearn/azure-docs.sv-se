---
title: Azure VMware-lösning enligt CloudSimple-Network check lista
description: Check lista för att allokera nätverks-CIDR på Azure VMware-lösning från CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c31942b198697ddc913f3732fd41409334ff0ef6
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817480"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Nätverks krav för Azure VMware-lösning av CloudSimple

Azure VMware-lösningen av CloudSimple erbjuder en privat VMware-moln miljö som är tillgänglig för användare och program från lokala miljöer, från företags hanterade enheter samt från Azure-resurser. Anslutningen levereras med nätverks tjänster som VPN-och ExpressRoute-anslutningar.  Vissa av de Network Services kräver att du anger nätverks adress intervall för att aktivera tjänsterna.  Tabellerna i den här artikeln beskriver en uppsättning adress intervall och motsvarande tjänster som använder de angivna adresserna.  Vissa adresser är obligatoriska och vissa beror på vilka tjänster du vill distribuera.  Dessa adress utrymmen ska inte överlappa något av dina lokala undernät, Azure Virtual Network-undernät eller planerade CloudSimple arbets belastnings undernät.

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>Nätverks adress intervall som krävs för att skapa ett privat moln

Under skapandet av CloudSimple-tjänsten och ett privat moln krävs följande CIDR-intervall för nätverk.

| Namn/Använd för     | Beskrivning                                                                                                                            | Adressintervall            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Gateway-CIDR      | Krävs för Edge Services (VPN-gatewayer).  Denna CIDR krävs när CloudSimple-tjänsten skapas och måste vara från RFC 1918-utrymmet. | /28                      |
| vSphere/virtuellt San CIDR | Krävs för VMware Management Networks. Denna CIDR måste anges när du skapar ett privat moln.                                    | /24 eller/23 eller/22 eller/21 |

## <a name="network-address-range-required-for-azure-network-connection-to-on-premises-network"></a>Nätverks adress intervall som krävs för Azures nätverks anslutning till ett lokalt nätverk

Anslutning från [det lokala nätverket till det privata moln nätverket med ExpressRoute](on-premises-connection.md) upprättar en global Reach anslutning.  Anslutningen utbyter vägar via BGP mellan ditt lokala nätverk, privata moln nätverk och dina Azure-nätverk.

| Namn/Använd för             | Beskrivning                                                                                                                                                                             | Adressintervall |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute peering CIDR | Krävs när du använder ExpressRoute Global Reach används för lokal anslutning. Denna CIDR måste anges när en Global Reach anslutningsbegäran görs via ett support ärende. | /29           |

## <a name="network-address-range-required-for-using-site-to-site-vpn-connection-to-on-premises-network"></a>Nätverks adress intervall krävs för att använda plats-till-plats-VPN-anslutning till lokalt nätverk

Anslutning från [det lokala nätverket till det privata moln nätverket med plats-till-plats-VPN](vpn-gateway.md) kräver följande IP-adresser, lokalt nätverk och identifierare. 

| Adress/adress intervall | Beskrivning                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Peer-IP               | Lokal VPN gateway offentlig IP-adress. Krävs för att upprätta plats-till-plats-VPN-anslutning mellan det lokala data centret och CloudSimple-tjänstens region. Den här IP-adressen krävs vid skapande av VPN gateway för plats-till-plats.                                         |
| Peer-identifierare       | Peer-ID för den lokala VPN-gatewayen. Detta är vanligt vis samma som **peer-IP**.  Om en unik identifierare anges på din lokala VPN-gateway måste identifieraren anges.  Peer-ID krävs vid skapande av plats-till-plats-VPN Gateway.   |
| Lokala nätverk   | Lokala prefix som behöver åtkomst till CloudSimple-nätverk i regionen.  Ta med alla prefix från det lokala nätverket som kommer att få åtkomst till CloudSimple-nätverket, inklusive klient nätverket där användarna kommer åt dem.                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>Nätverks adress intervall som krävs för att använda punkt-till-plats-VPN-anslutningar

Punkt-till-plats-VPN-anslutning ger åtkomst till CloudSimple-nätverket från en klient dator.  [Konfiguration av punkt-till-plats-VPN](vpn-gateway.md) kräver att följande nätverks adress intervall anges.

| Adress/adress intervall | Beskrivning                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Klient under nät         | DHCP-adresser tilldelas från klient under nätet när du ansluter med punkt-till-plats-VPN. Det här under nätet krävs när du skapar en punkt-till-plats-VPN-gateway på CloudSimple-portalen.  Nätverket kommer att delas upp i två undernät och ett används för UDP-anslutning och andra för TCP-anslutningar. |

## <a name="next-steps"></a>Nästa steg

* [Snabb start – skapa CloudSimple-tjänst](quickstart-create-cloudsimple-service.md)
* [snabb start – skapa – privat – moln](quickstart-create-private-cloud.md)
* Läs mer om [Azures nätverks anslutningar](cloudsimple-azure-network-connection.md)
* Läs mer om [VPN-gatewayer](cloudsimple-vpn-gateways.md)
