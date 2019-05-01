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
ms.openlocfilehash: fa7730247ddc5f30c3d21a32421a6c55ec4ef72e
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872770"
---
# <a name="vpn-gateways-overview"></a>Översikt över VPN-gatewayer

En VPN-gateway används för att skicka krypterad trafik mellan ett CloudSimple region nätverk med en lokal plats eller en dator via det offentliga Internet.  Varje region kan bara ha en VPN-gateway. Du kan dock skapa flera anslutningar till samma VPN-gateway. När du skapar flera anslutningar till samma VPN-gateway delar alla VPN-tunnlar på den tillgängliga bandbredden.

CloudSimple innehåller två typer av VPN-gatewayer:

* Plats-till-plats-VPN-Gateway
* Punkt-till-plats-VPN-Gateway

## <a name="site-to-site-vpn-gateway"></a>Plats-till-plats VPN-gateway

En plats-till-plats VPN-gateway används för att skicka krypterad trafik mellan ett CloudSimple region nätverk och ett lokalt datacenter. Använd den här anslutningen för att definiera undernät/CIDR-intervall för nätverkstrafik mellan ditt lokala nätverk och CloudSimple region nätverk.

VPN-gatewayen kan du använda tjänster från en lokal plats i ditt privata moln och tjänster i ditt privata moln, från det lokala nätverket.  CloudSimple ger en principbaserad VPN-server för att upprätta anslutning från ditt lokala nätverk.

Användningsområden för plats-till-plats-VPN är:

* Tillgängligheten för dina privata moln vCenter från alla arbetsstationer i ditt lokala nätverk.
* Användning av din lokala Active Directory som en källa för vCenter-identitet.
* Praktiskt överföring av VM-mallar, ISO och andra filer från din lokala resurser till ditt privata moln vCenter.
* Tillgängligheten för arbetsbelastningar som körs i ditt privata moln från ditt lokala nätverk.

### <a name="cryptographic-parameters"></a>Kryptografiska parametrarna

En plats-till-plats VPN-anslutning använder följande kryptografiska standardparametrar för att upprätta en säker anslutning.  När du skapar en anslutning från den lokala VPN-enheten måste matcha parametrarna.

En plats-till-plats VPN-anslutning använder följande kryptografiska standardparametrar för att upprätta en säker anslutning.  När du skapar en anslutning från den lokala VPN-enhet kan du använda någon av följande parametrar som stöds av din lokala VPN-gateway.

#### <a name="phase-1-proposals"></a>Fas 1 förslag

| Parameter | Förslag 1 | Förslag 2 | Förslag 3 |
|-----------|------------|------------|------------|
| IKE-version | IKEv1 | IKEv1 | IKEv1 |
| Kryptering | AES 128 | AES-256 | AES-256 |
| Hash-algoritm| SHA 256 | SHA 256 | SHA 1 |
| Diffie-Hellman-grupp (DH grupp) | 1 | 1 | 1 |
| Livslängd | 28 800 sekunder | 28 800 sekunder | 28 800 sekunder |
| Datastorlek | 4 GB | 4 GB | 4 GB |


#### <a name="phase-2-proposals"></a>Fas 2 förslag 

| Parameter | Förslag 1 | Förslag 2 | Förslag 3 |
|-----------|------------|------------|------------|
| Kryptering | AES 128 | AES-256 | AES-256 |
| Hash-algoritm| SHA 256 | SHA 256 | SHA 1 |
| Perfekt vanlig PFS-grupp (PFS-grupp) | Ingen | Ingen | Ingen |
| Livslängd | 1 800 sekunder | 1 800 sekunder | 1 800 sekunder |
| Datastorlek | 4 GB | 4 GB | 4 GB |

## <a name="point-to-site-vpn-gateway"></a>Punkt-till-plats VPN-gateway

En punkt-till-plats VPN-anslutning används för att skicka krypterad trafik mellan ett CloudSimple region nätverk och en klientdator.  Punkt-till-plats-VPN är det enklaste sättet att komma åt nätverket privat moln, inklusive dina privata moln vCenter och virtuella datorerna i arbetsbelastningen.  Använd punkt-till-plats VPN-anslutning om du ansluter till det privata molnet via en fjärranslutning.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera VPN-gateway](https://docs.azure.cloudsimple.com/vpn-gateway/)