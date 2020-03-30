---
title: Azure VMware-lösning från CloudSimple - VPN-gateways
description: Lär dig mer om CloudSimple-VPN-koncept för plats-till-plats och VPN-koncept för punkt till plats
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 662fa342b3a18f726b418c496ff3fda937445301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024865"
---
# <a name="vpn-gateways-overview"></a>Översikt över VPN-gateways

En VPN-gateway används för att skicka krypterad trafik mellan ett CloudSimple-regionnätverk på en lokal plats eller en dator via det offentliga internet.  Varje region kan ha en VPN-gateway, som kan stödja flera anslutningar. När du skapar flera anslutningar till samma VPN-gateway delar alla VPN-tunnlar på den tillgängliga bandbredden.

CloudSimple tillhandahåller två typer av VPN-gateways:

* VPN-gateway för plats till plats
* VPN-gateway för punkt till plats

## <a name="site-to-site-vpn-gateway"></a>VPN-gateway för plats till plats

En VPN-gateway från plats till plats används för att skicka krypterad trafik mellan ett CloudSimple-regionnätverk och ett lokalt datacenter. Använd den här anslutningen för att definiera undernät/CIDR-intervall för nätverkstrafik mellan det lokala nätverket och CloudSimple-regionnätverket.

Med VPN-gatewayen kan du använda tjänster från lokalt i ditt privata moln och tjänster i ditt privata moln från det lokala nätverket.  CloudSimple tillhandahåller en principbaserad VPN-server för upprättande av anslutningen från ditt lokala nätverk.

Användningsfall för VPN från plats till plats:

* Hjälpmedel för ditt privata moln vCenter från alla arbetsstationer i ditt lokala nätverk.
* Användning av din lokala Active Directory som en vCenter-identitetskälla.
* Bekväm överföring av VM-mallar, Iso-filer och andra filer från lokala resurser till ditt privata moln vCenter.
* Hjälpmedel för arbetsbelastningar som körs i ditt privata moln från ditt lokala nätverk.

![Topologi för VPN-anslutning från plats till plats](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Kryptografiska parametrar

En VPN-anslutning från plats till plats använder följande standardkryptografiiska parametrar för att upprätta en säker anslutning.  När du skapar en anslutning från din lokala VPN-enhet använder du någon av följande parametrar som stöds av din lokala VPN-gateway.

#### <a name="phase-1-proposals"></a>Förslag till fas 1

| Parameter | Förslag 1 | Förslag 2 | Förslag 3 |
|-----------|------------|------------|------------|
| IKE-version | IKEv1 | IKEv1 | IKEv1 |
| Kryptering | AES 128 | AES 256 | AES 256 |
| Hash-algoritm| SHA 256 | SHA 256 | SHA 1 (PÅ) |
| Diffie Hellman-gruppen (DH-gruppen) | 2 | 2 | 2 |
| Livstid | 28 800 sekunder | 28 800 sekunder | 28 800 sekunder |
| Datastorlek | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>Förslag till fas 2

| Parameter | Förslag 1 | Förslag 2 | Förslag 3 |
|-----------|------------|------------|------------|
| Kryptering | AES 128 | AES 256 | AES 256 |
| Hash-algoritm| SHA 256 | SHA 256 | SHA 1 (PÅ) |
| Perfect Forward Secrecy Group (PFS Group) | Inget | Inget | Inget |
| Livstid | 1 800 sekunder | 1 800 sekunder | 1 800 sekunder |
| Datastorlek | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> Ställ in TCP MSS Clamping vid 1200 på din VPN-enhet. Eller om dina VPN-enheter inte stöder MSS-fastspänning kan du alternativt ställa in MTU på tunnelgränssnittet på 1240 byte istället.

## <a name="point-to-site-vpn-gateway"></a>VPN-gateway för punkt till plats

En punkt-till-plats-VPN används för att skicka krypterad trafik mellan ett CloudSimple-regionnätverk och en klientdator.  Point-to-Site VPN är det enklaste sättet att komma åt ditt privata molnnätverk, inklusive ditt privata molnvCenter och virtuella virtuella arbetsbelastningar.  Använd Punkt-till-plats VPN-anslutning om du ansluter till det privata molnet på distans.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera VPN-gateway](vpn-gateway.md)
