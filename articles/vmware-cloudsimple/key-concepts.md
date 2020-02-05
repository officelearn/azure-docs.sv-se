---
title: Viktiga begrepp för administration av Azure VMware-lösningar (AVS)
description: Beskriver viktiga begrepp för administration av Azure VMware-lösningar (AVS)
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c581ffbba7fbaefc8fbd0ffc7e92350bd69802b8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025239"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-avs"></a>Viktiga begrepp för administration av Azure VMware-lösningar (AVS)

Administration av Azure VMware-lösningar (AVS) kräver en förståelse av följande begrepp:

* AVS-tjänst, som visas som Azure VMware-lösningar (AVS)-service
* AVS-nod, som visas som Azure VMware-lösningar (AVS)-nod
* AVS-privat moln
* Tjänst nätverk
* AVS virtuell dator, som visas som Azure VMware-lösningar (AVS) – virtuell dator

## <a name="avs-service"></a>AVS-tjänst

Med AVS-tjänsten kan du skapa och hantera alla resurser som är associerade med VMware-lösningar (AVS) från Azure Portal. Skapa en tjänst resurs i varje region där du tänker använda tjänsten.

Läs mer om [AVS-tjänsten](cloudsimple-service.md).

## <a name="avs-node"></a>AVS-nod

En AVS-nod är en dedikerad, Bare Metal, konvergerad beräknings-och lagrings värd där VMware ESXi hypervisor-modulen distribueras. Den här noden ingår sedan i VMware vSphere-, vCenter-, virtuellt San-och NSX-plattformarna. AVS nätverks tjänster och Edge Networking Services är också aktiverade. Varje nod fungerar som en enhet med beräknings-och lagrings kapacitet som du kan etablera för att skapa [AVS-privata moln](cloudsimple-private-cloud.md). Du etablerar eller reserverar noder i en region där AVS-tjänsten är tillgänglig.

Läs mer om [AVS-noder](cloudsimple-node.md).

## <a name="avs-private-cloud"></a>AVS-privat moln

Ett privat AVS-moln är en isolerad VMware stack-miljö som hanteras av en vCenter-Server i en egen hanterings domän. VMware-stacken innehåller ESXi-värdar, vSphere, vCenter, virtuellt San och NSX. Stacken körs på dedikerade noder (dedikerad och isolerad Bare Metal-maskinvara) och används av användare via inbyggda VMware-verktyg som innehåller vCenter och NSX Manager. Dedikerade noder distribueras på Azure-platser och hanteras av Azure. Varje AVS-privat moln kan segmenteras och skyddas med hjälp av nätverks tjänster som VLAN och undernät och brand Väggs tabeller. Anslutningar till din lokala miljö och Azure-nätverket skapas med hjälp av säkra, privata VPN-och Azure ExpressRoute-anslutningar.

Läs mer om det [privata molnet i AVS](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Tjänst nätverk

AVS-tjänsten tillhandahåller ett nätverk per region där din AVS-tjänst distribueras. Nätverket är ett enskilt adress utrymme för TCP-skikt 3 med Routning aktiverat som standard. Alla moln-och undernät i AVS som skapats i den här regionen kommunicerar med varandra utan ytterligare konfiguration. Du skapar distribuerade Port grupper på vCenter med hjälp av VLAN. Du kan använda följande nätverksfunktioner för att konfigurera och skydda dina arbets belastnings resurser i ditt AVS-privata moln:

* [VLAN och undernät](cloudsimple-vlans-subnets.md)
* [Brand Väggs tabeller](cloudsimple-firewall-tables.md)
* [VPN-gatewayer](cloudsimple-vpn-gateways.md)
* [Offentlig IP-adress](cloudsimple-public-ip-address.md)
* [Azure-nätverksanslutning](cloudsimple-azure-network-connection.md)

## <a name="avs-virtual-machine"></a>Virtuell AVS-dator

Med AVS-tjänsten kan du hantera virtuella VMware-datorer från Azure Portal. Ett eller flera kluster eller resurspooler från din vSphere-miljö kan mappas till den prenumeration där tjänsten skapas.

Läs mer om:

* [Virtuella AVS-datorer](cloudsimple-virtual-machines.md)
* [Mappning av Azure-prenumeration](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
