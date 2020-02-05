---
title: Azure VMware-lösningar (AVS) – VPN-gatewayer
description: Lär dig mer om begrepp för plats-till-plats-VPN och punkt-till-plats VPN-koncept
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 73171e2c46bdf6c934db5777efe36ba51153a686
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024865"
---
# <a name="vpn-gateways-overview"></a>Översikt över VPN-gatewayer

En VPN-gateway används för att skicka krypterad trafik mellan ett AVS-områdes nätverk på en lokal plats eller en dator via det offentliga Internet. Varje region kan ha en VPN-gateway som har stöd för flera anslutningar. När du skapar flera anslutningar till samma VPN-gateway delar alla VPN-tunnlar på den tillgängliga bandbredden.

AVS tillhandahåller två typer av VPN-gatewayer:

* Plats-till-plats-VPN-gateway
* Punkt-till-plats-VPN-gateway

## <a name="site-to-site-vpn-gateway"></a>Plats-till-plats-VPN-gateway

En plats-till-plats-VPN-gateway används för att skicka krypterad trafik mellan ett nätverk för AVS-regioner och ett lokalt Data Center. Använd den här anslutningen för att definiera undernät/CIDR-intervall för nätverks trafik mellan ditt lokala nätverk och nätverket i AVS-regionen.

VPN-gatewayen gör att du kan använda tjänster lokalt i ditt molns privata moln och tjänster i ditt moln privata moln från det lokala nätverket. AVS tillhandahåller en principbaserad VPN-server för att upprätta anslutningen från ditt lokala nätverk.

Användnings fall för plats-till-plats-VPN:

* Tillgänglighet för ditt moln privata moln vCenter från valfri arbets station i ditt lokala nätverk.
* Användning av din lokala Active Directory som vCenter-identitets källa.
* Bekväm överföring av VM-mallar, ISO och andra filer från dina lokala resurser till ditt molns privata moln vCenter.
* Tillgänglighet för arbets belastningar som körs på ditt moln privata moln från ditt lokala nätverk.

![Topologi för plats-till-plats-VPN-anslutning](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Kryptografiska parametrar

En plats-till-plats-VPN-anslutning använder följande kryptografiska standard parametrar för att upprätta en säker anslutning. När du skapar en anslutning från din lokala VPN-enhet använder du någon av följande parametrar som stöds av din lokala VPN-gateway.

#### <a name="phase-1-proposals"></a>Fas 1-förslag

| Parameter | Förslag 1 | Förslag 2 | Förslag 3 |
|-----------|------------|------------|------------|
| IKE-version | IKEv1 | IKEv1 | IKEv1 |
| Kryptering | AES 128 | AES 256 | AES 256 |
| Hash-algoritm| SHA 256 | SHA 256 | SHA 1 |
| Diffie Hellman-grupp (DH-grupp) | 2 | 2 | 2 |
| Livs längd | 28 800 sekunder | 28 800 sekunder | 28 800 sekunder |
| Data storlek | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>Fas 2-förslag

| Parameter | Förslag 1 | Förslag 2 | Förslag 3 |
|-----------|------------|------------|------------|
| Kryptering | AES 128 | AES 256 | AES 256 |
| Hash-algoritm| SHA 256 | SHA 256 | SHA 1 |
| PFS-grupp (Perfect Forward Secrecy) | Inget | Inget | Inget |
| Livs längd | 1 800 sekunder | 1 800 sekunder | 1 800 sekunder |
| Data storlek | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> Ange TCP MSS-ihopfogning vid 1200 på VPN-enheten. Eller om VPN-enheterna inte stöder MSS-ihopfogning kan du i stället ange MTU för tunnel gränssnittet till 1240 byte.

## <a name="point-to-site-vpn-gateway"></a>Punkt-till-plats-VPN-gateway

En punkt-till-plats-VPN används för att skicka krypterad trafik mellan ett AVS-områdes nätverk och en klient dator. Punkt-till-plats-VPN är det enklaste sättet att få åtkomst till ditt moln nätverk i molnet, inklusive ditt molns privata moln vCenter och virtuella datorer för arbets belastning. Använd punkt-till-plats-VPN-anslutning om du ansluter till det privata moln molnet via en fjärr anslutning.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera VPN gateway](vpn-gateway.md)
