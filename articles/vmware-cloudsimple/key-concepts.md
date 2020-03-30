---
title: Viktiga begrepp för att administrera Azure VMware-lösning by CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: Beskriver viktiga begrepp för att administrera Azure VMware Solutions by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efe6cc132b062e833dc85c13cf2f6c5f6289484c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564627"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>Nyckelbegrepp för administration av Azure VMware Solutions by CloudSimple

Att administrera Azure VMware Solutions by CloudSimple kräver en förståelse för följande begrepp:

* CloudSimple-tjänsten, som visas som Azure VMware Solutions by CloudSimple - Service
* CloudSimple-nod, som visas som Azure VMware Solutions by CloudSimple - Node
* CloudSimple privat moln
* Nätverk av tjänster
* CloudSimple virtuell dator, som visas som Azure VMware Solutions av CloudSimple - Virtuell dator

## <a name="cloudsimple-service"></a>CloudSimple-tjänst

Med CloudSimple-tjänsten kan du skapa och hantera alla resurser som är associerade med VMware Solutions by CloudSimple från Azure-portalen. Skapa en tjänstresurs i alla regioner där du tänker använda tjänsten.

Läs mer om [CloudSimple-tjänsten](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>CloudSimple-nod

En CloudSimple-nod är en dedikerad, bare-metal, hyperconverged compute and storage host som VMware ESXi hypervisor distribueras till. Den här noden ingår sedan i plattformarna VMware vSphere, vCenter, vSAN och NSX. CloudSimple-nätverkstjänster och edge-nätverkstjänster är också aktiverade. Varje nod fungerar som en enhet med beräknings- och lagringskapacitet som du kan etablera för att skapa [CloudSimple privata moln](cloudsimple-private-cloud.md). Du etablerar eller reserverar noder i en region där CloudSimple-tjänsten är tillgänglig.

Läs mer om [CloudSimple-noder](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>CloudSimple privat moln

Ett CloudSimple privat moln är en isolerad VMware stack miljö som hanteras av en vCenter-server i sin egen hanteringsdomän. VMware-stacken innehåller ESXi-värdar, vSphere, vCenter, vSAN och NSX. Stacken körs på dedikerade noder (dedikerad och isolerad bare-metal hårdvara) och förbrukas av användare via inbyggda VMware-verktyg som inkluderar vCenter och NSX Manager. Dedikerade noder distribueras på Azure-platser och hanteras av Azure. Varje privat moln kan segmenteras och skyddas med hjälp av nätverkstjänster som VLAN och undernät och brandväggstabeller. Anslutningar till din lokala miljö och Azure-nätverket skapas med hjälp av säkra, privata VPN- och Azure ExpressRoute-anslutningar.

Läs mer om [CloudSimple privat moln](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Nätverk av tjänster

CloudSimple-tjänsten tillhandahåller ett nätverk per region där din CloudSimple-tjänst distribueras. Nätverket är ett enda TCP Layer 3-adressutrymme med routning aktiverat som standard. Alla privata moln och undernät som skapas i den här regionen kommunicerar med varandra utan någon ytterligare konfiguration. Du skapar distribuerade portgrupper på vCenter med hjälp av VLAN.You create distributed port groups on the vCenter by using the VLAN.You create distributed port groups on the vCenter by using the VLANNs. Du kan använda följande nätverksfunktioner för att konfigurera och skydda dina arbetsbelastningsresurser i ditt privata moln:

* [VLAN och undernät](cloudsimple-vlans-subnets.md)
* [Brandväggstabeller](cloudsimple-firewall-tables.md)
* [VPN-gateways](cloudsimple-vpn-gateways.md)
* [Offentlig IP](cloudsimple-public-ip-address.md)
* [Azure-nätverksanslutning](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Virtuella CloudSimple-datorer

Med CloudSimple-tjänsten kan du hantera virtuella VMware-datorer från Azure-portalen. Ett eller flera kluster eller resurspooler från din vSphere-miljö kan mappas till den prenumeration som tjänsten skapas på.

Läs mer om:

* [Virtuella CloudSimple-datorer](cloudsimple-virtual-machines.md)
* [Azure-prenumerationsmappning](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
