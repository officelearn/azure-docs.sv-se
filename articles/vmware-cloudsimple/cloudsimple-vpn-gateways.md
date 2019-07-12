---
title: VPN-gatewayer i VMware-lösning genom CloudSimple - Azure
description: Läs mer om CloudSimple VPN för plats-till-plats och punkt-till-plats VPN-begrepp
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2eae81f357904bd5034d7409ef42b681d1085930
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695222"
---
# <a name="vpn-gateways-overview"></a>Översikt över VPN-gatewayer

En VPN-gateway används för att skicka krypterad trafik mellan ett CloudSimple region nätverk med en lokal plats eller en dator via det offentliga Internet.  Varje region kan bara ha en VPN-gateway. Du kan dock skapa flera anslutningar till samma VPN-gateway. När du skapar flera anslutningar till samma VPN-gateway delar alla VPN-tunnlar på den tillgängliga bandbredden.

CloudSimple innehåller två typer av VPN-gatewayer:

* Plats-till-plats-VPN-Gateway
* Punkt-till-plats-VPN-Gateway

## <a name="site-to-site-vpn-gateway"></a>Plats-till-plats VPN-gateway

En plats-till-plats VPN-gateway används för att skicka krypterad trafik mellan ett CloudSimple region nätverk och ett lokalt datacenter. Använd den här anslutningen för att definiera undernät/CIDR-intervall för kommunikation mellan ditt lokala nätverk och CloudSimple region nätverket.

VPN-gatewayen kan du använda tjänster från en lokal plats i ditt privata moln och tjänster i ditt privata moln, från det lokala nätverket.  CloudSimple ger en principbaserad VPN-server för att upprätta anslutning från ditt lokala nätverk.

Användningsområden för plats-till-plats-VPN är:

* Tillgängligheten för dina privata moln vCenter från alla arbetsstationer i ditt lokala nätverk.
* Användning av din lokala Active Directory som en källa för vCenter-identitet.
* Praktiskt överföring av VM-mallar, ISO och andra filer från din lokala resurser till ditt privata moln vCenter.
* Tillgängligheten för arbetsbelastningar som körs i ditt privata moln från ditt lokala nätverk.

![Topologi för plats-till-plats VPN-anslutning](media/cloudsimple-site-to-site-vpn-connection.png)

> [!IMPORTANT]
> Du måste foga ihop TCP MSS vid 1078 byte eller lägre. Eller om dina VPN-enheter inte stöder MSS-ihopfogning kan du även ange MTU i tunnelgränssnittet till 1118 byte i stället. 

### <a name="cryptographic-parameters"></a>Kryptografiska parametrarna

En plats-till-plats VPN-anslutning använder följande kryptografiska standardparametrar för att upprätta en säker anslutning.  När du skapar en anslutning från den lokala VPN-enhet kan du använda någon av följande parametrar som stöds av din lokala VPN-gateway.

#### <a name="phase-1-proposals"></a>Fas 1 förslag

| Parameter | Förslag 1 | Förslag 2 | Förslag 3 |
|-----------|------------|------------|------------|
| IKE-version | IKEv1 | IKEv1 | IKEv1 |
| Kryptering | AES 128 | AES 256 | AES 256 |
| Hash-algoritm| SHA 256 | SHA 256 | SHA 1 |
| Diffie-Hellman-grupp (DH grupp) | 2 | 2 | 2 |
| Livslängd | 28 800 sekunder | 28 800 sekunder | 28 800 sekunder |
| Datastorlek | 4 GB | 4 GB | 4 GB |


#### <a name="phase-2-proposals"></a>Fas 2 förslag 

| Parameter | Förslag 1 | Förslag 2 | Förslag 3 |
|-----------|------------|------------|------------|
| Kryptering | AES 128 | AES 256 | AES 256 |
| Hash-algoritm| SHA 256 | SHA 256 | SHA 1 |
| Perfekt vanlig PFS-grupp (PFS-grupp) | Inga | Ingen | Ingen |
| Livslängd | 1 800 sekunder | 1 800 sekunder | 1 800 sekunder |
| Datastorlek | 4 GB | 4 GB | 4 GB |

## <a name="point-to-site-vpn-gateway"></a>Punkt-till-plats VPN-gateway

En punkt-till-plats VPN-anslutning används för att skicka krypterad trafik mellan ett CloudSimple region nätverk och en klientdator.  Punkt-till-plats-VPN är det enklaste sättet att komma åt nätverket privat moln, inklusive dina privata moln vCenter och virtuella datorerna i arbetsbelastningen.  Använd punkt-till-plats VPN-anslutning om du ansluter till det privata molnet via en fjärranslutning.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera VPN-gateway](https://docs.azure.cloudsimple.com/vpn-gateway/)