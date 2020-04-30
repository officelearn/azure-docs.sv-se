---
title: Viktiga begrepp för att administrera en Azure VMware-lösning av CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: Beskriver viktiga begrepp för administration av Azure VMware-lösningar från CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 410215550f728d6b11b02b3ef108d429481e42a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869032"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>Viktiga begrepp för administration av Azure VMware-lösningar från CloudSimple

Att administrera Azure VMware-lösningar av CloudSimple kräver att du förstår följande begrepp:

* CloudSimple-tjänsten, som visas som Azure VMware-lösningar av CloudSimple-service
* CloudSimple-nod, som visas som Azure VMware-lösningar av CloudSimple-Node
* CloudSimple privat moln
* Tjänst nätverk
* CloudSimple virtuell dator, som visas som Azure VMware-lösningar av CloudSimple-virtuell dator

## <a name="cloudsimple-service"></a>CloudSimple-tjänst

Med CloudSimple-tjänsten kan du skapa och hantera alla resurser som är associerade med VMware-lösningar från CloudSimple från Azure Portal. Skapa en tjänst resurs i varje region där du tänker använda tjänsten.

Läs mer om [CloudSimple-tjänsten](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>CloudSimple-nod

En CloudSimple-nod är en dedikerad, Bare Metal, konvergerad beräknings-och lagrings värd där VMware ESXi hypervisor-modulen distribueras. Den här noden ingår sedan i VMware vSphere-, vCenter-, virtuellt San-och NSX-plattformarna. CloudSimple Networking Services och Edge Networking Services är också aktiverade. Varje nod fungerar som en enhet med beräknings-och lagrings kapacitet som du kan etablera för att skapa [CloudSimple privata moln](cloudsimple-private-cloud.md). Du etablerar eller reserverar noder i en region där CloudSimple-tjänsten är tillgänglig.

Läs mer om [CloudSimple-noder](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>CloudSimple privat moln

Ett privat CloudSimple-moln är en isolerad VMware stack-miljö som hanteras av en vCenter-Server i en egen hanterings domän. VMware-stacken innehåller ESXi-värdar, vSphere, vCenter, virtuellt San och NSX. Stacken körs på dedikerade noder (dedikerad och isolerad Bare Metal-maskinvara) och används av användare via inbyggda VMware-verktyg som innehåller vCenter och NSX Manager. Dedikerade noder distribueras på Azure-platser och hanteras av Azure. Varje privat moln kan segmenteras och skyddas med hjälp av nätverks tjänster som VLAN och undernät och brand Väggs tabeller. Anslutningar till din lokala miljö och Azure-nätverket skapas med hjälp av säkra, privata VPN-och Azure ExpressRoute-anslutningar.

Lär dig mer om [CloudSimple privat moln](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Tjänst nätverk

CloudSimple-tjänsten tillhandahåller ett nätverk per region där din CloudSimple-tjänst har distribuerats. Nätverket är ett enskilt adress utrymme för TCP-skikt 3 med Routning aktiverat som standard. Alla privata moln och undernät som skapats i den här regionen kommunicerar med varandra utan ytterligare konfiguration. Du skapar distribuerade Port grupper på vCenter med hjälp av VLAN. Du kan använda följande nätverks funktioner för att konfigurera och skydda dina arbets belastnings resurser i ditt privata moln:

* [VLAN och undernät](cloudsimple-vlans-subnets.md)
* [Brandväggstabeller](cloudsimple-firewall-tables.md)
* [VPN-gatewayer](cloudsimple-vpn-gateways.md)
* [Offentlig IP](cloudsimple-public-ip-address.md)
* [Azure-nätverksanslutning](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>CloudSimple virtuell dator

Med CloudSimple-tjänsten kan du hantera virtuella VMware-datorer från Azure Portal. Ett eller flera kluster eller resurspooler från din vSphere-miljö kan mappas till den prenumeration där tjänsten skapas.

Läs mer om:

* [Virtuella CloudSimple-datorer](cloudsimple-virtual-machines.md)
* [Mappning av Azure-prenumeration](https://docs.microsoft.com/azure/vmware-cloudsimple/azure-subscription-mapping/)
