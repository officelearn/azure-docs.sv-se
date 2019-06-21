---
title: Viktiga begrepp för att administrera Azure VMware-lösning genom CloudSimple
description: Beskriver viktiga begrepp för att administrera Azure VMware-lösning genom CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3eff61408cb190396987ace6dee21182cff4f25c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165195"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Viktiga begrepp för administration av Azure VMware-lösning genom CloudSimple

Administrera Azure VMware-lösningen av CloudSimple kräver kunskap om följande begrepp:

* CloudSimple-tjänsten, som visas som Azure VMware-lösningen av CloudSimple - tjänsten
* CloudSimple nod som visas som Azure VMware-lösningen av CloudSimple - nod
* CloudSimple privat moln
* Tjänsten nätverk
* CloudSimple virtuell dator, som visas som Azure VMware-lösningen som CloudSimple - VM

## <a name="cloudsimple-service"></a>CloudSimple service

Med tjänsten CloudSimple, kan du skapa och hantera alla resurser som är associerad med VMware-lösningar genom CloudSimple från Azure-portalen. Skapa en tjänstresurs i varje region där du planerar att använda tjänsten.

Läs mer om den [CloudSimple service](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>CloudSimple nod

En CloudSimple nod är en dedikerad, utan operativsystem, hyperkonvergerat beräknings- och värden som dit VMware ESXi-hypervisor-programmet har distribuerats. Den här noden ingår sedan i VMware vSphere, vCenter, virtuellt SAN-nätverk och NSX plattformar. CloudSimple nätverkstjänster och edge nätverkstjänster aktiveras också. Varje nod som fungerar som en enhet med kapacitet för beräkning och lagring som du kan utnyttja för att skapa [CloudSimple privata moln](cloudsimple-private-cloud.md). Etablerar eller reservera noder i en region där CloudSimple-tjänsten är tillgänglig.


Läs mer om [CloudSimple noder](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>CloudSimple privat moln

Ett privat moln CloudSimple är en isolerad VMware stack miljö som hanteras av en vCenter-server i sin egen hanteringsdomänen. VMware-stacken innehåller ESXi-värdar, vSphere, vCenter, virtuellt SAN-nätverk och NSX. Stack körs på dedikerade noder (dedikerad och isolerad bare metal-maskinvara) och används av användare med hjälp av inbyggda VMware-verktyg som innehåller vCenter och NSX Manager. Dedikerade noder distribueras i Azure-platser och hanteras av Azure. Varje privata moln kan segmenterade och skyddas med hjälp av nätverkstjänster, till exempel VLAN och undernät och brandväggen tabeller. Anslutningar till din lokala miljö och Azure-nätverk skapas genom att använda säkra, privata VPN och Azure ExpressRoute anslutningar.

Läs mer om [CloudSimple optimeras](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Tjänsten nätverk

Tjänsten CloudSimple ger en nätverket för varje region där din CloudSimple-tjänsten har distribuerats. Nätverket är ett enda TCP Layer 3-adressutrymme med routning aktiverad som standard. Alla privata moln och undernät som skapats i den här regionen kan du kommunicera med varandra utan någon ytterligare konfiguration. Du kan skapa distribuerade portgrupper på vCenter med hjälp av de virtuella lokala nätverken. Du kan använda följande nätverksfunktioner för att konfigurera och skydda arbetsbelastning resurser i ditt privata moln:

* [VLAN och undernät](cloudsimple-vlans-subnets.md)
* [Brandväggen tabeller](cloudsimple-firewall-tables.md)
* [VPN-gatewayer](cloudsimple-vpn-gateways.md)
* [Offentlig IP-adress](cloudsimple-public-ip-address.md)
* [Azure nätverksanslutning](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>CloudSimple virtuell dator

Du kan hantera virtuella VMware-datorer med CloudSimple-tjänsten från Azure-portalen. En eller flera kluster eller resurspooler från miljön vSphere kan mappas till den prenumeration där tjänsten skapas.

Läs mer om:

* [CloudSimple virtuella datorer](cloudsimple-virtual-machines.md)
* [Mappning av Azure-prenumeration](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
