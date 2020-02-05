---
title: Se till att programmet är högt tillgängligt när du kör i VMware på Azure
description: Beskriver funktioner i AVS-hög tillgänglighet för att åtgärda vanliga program krascher för program som körs i ett privat moln i molnet
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b32f7f3f38098f935382cce46d8251340784b940
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025358"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Se till att programmet är högt tillgängligt när du kör i VMware på Azure

AVS-lösningen ger hög tillgänglighet för dina program som körs på VMware i Azure-miljön. I följande tabell visas de olika felen och de associerade funktionerna för hög tillgänglighet.

| Scenario för haveri | Program skyddat? | Plattform HA-funktion | VMware HA-funktion | Azure HA-funktion |
------------ | ------------- | ------------ | ------------ | ------------- |
| Diskfel | Ja | Snabb ersättning av misslyckad nod | [Om standard lagrings principen för virtuellt San](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| Fläkt haveri | Ja | Redundanta fläktar, snabb ersättning av misslyckad nod |  |  |
| NIC-problem | Ja | Redundant NIC, snabb ersättning av misslyckad nod
| Värd strömförsörjnings problem | Ja | Redundant strömförsörjning |  |  |
| ESXi-värd haveri | Ja | snabb ersättning av misslyckad nod | [VMware vSphere hög tillgänglighet](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| VM-haveri | Ja | [Lastbalanserare](load-balancers.md)  | [VMware vSphere hög tillgänglighet](https://www.vmware.com/products/vsphere/high-availability.html) | Azure Load Balancer för tillstånds lösa virtuella VMware-datorer |
| Port haveri för löv växel | Ja | Redundant NIC |  |  |
| Löv växlings problem | Ja | Redundanta löv växlar |  |  |
| Rack Haveriering | Ja | Placeringsgrupper |  |  |
| Nätverks anslutning till lokal DOMÄNKONTROLLANT | Ja  | Redundanta nätverks tjänster |  | Redundanta ER-kretsar |
| Nätverks anslutning till Azure | Ja | |  | Redundanta ER-kretsar |
| Data Center haveri | Ja |  |  | Tillgänglighetszoner |
| Regionalt haveri | Ja  |  |  | Azure-regioner |

Azure VMware-lösningen enligt AVS tillhandahåller följande funktioner för hög tillgänglighet.

## <a name="fast-replacement-of-failed-node"></a>Snabb ersättning av misslyckad nod

Program varan för AVS-kontrollens plan övervakar kontinuerligt hälso tillståndet för VMware-kluster och identifierar när en ESXi-nod Miss lyckas. Den lägger sedan automatiskt till en ny ESXi-värd till det berörda VMware-klustret från sin pool med lättillgängliga noder och tar den felande noden från klustret. Den här funktionen säkerställer att den extra kapaciteten i VMware-klustret återställs snabbt så att klustrets återhämtnings förmåga från virtuellt San och VMware HA återställts.

## <a name="placement-groups"></a>Placeringsgrupper

En användare som skapar ett privat AVS-moln kan välja en Azure-region och en placerings grupp i den valda regionen. En placerings grupp är en uppsättning noder som sprids över flera rack, men inom samma rygg nätverks segment. Noder inom samma placerings grupp kan komma åt varandra med högst två extra växel hopp. En placerings grupp är alltid inom en enda Azure-tillgänglighets zon och omfattar flera rack. AVS-kontroll planet distribuerar noder i ett moln privat moln över flera rack baserat på bästa möjliga ansträngning. Noder i olika placerings grupper är garanterat placerade i olika rack.

## <a name="availability-zones"></a>Tillgänglighetszoner

Tillgänglighets zoner är ett erbjudande med hög tillgänglighet som skyddar dina program och data från data Center problem. Tillgänglighets zoner är särskilda fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. Varje region har en tillgänglighets zon. Mer information finns i [Vad är Tillgänglighetszoner i Azure?](../availability-zones/az-overview.md).

## <a name="redundant-azure-expressroute-circuits"></a>Redundanta Azure ExpressRoute-kretsar

Data Center anslutningen till Azure vNet med ExpressRoute har redundanta kretsar för att tillhandahålla anslutning med hög tillgängliga nätverks anslutningar.

## <a name="redundant-networking-services"></a>Redundanta nätverks tjänster

Alla AVS-nätverksfunktioner för molnets privata moln (inklusive VLAN, brand vägg, offentliga IP-adresser, Internet och VPN) är utformade för att vara hög tillgängliga och kunna stödja service avtalet för tjänster.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Azure Layer 7-Load Balancer för tillstånds lösa virtuella VMware-datorer

Användare kan lägga till ett Azure Layer 7-Load Balancer framför de tillstånds lösa virtuella datorer i VMware-miljön som körs i VMware-miljön för att uppnå hög tillgänglighet för webb nivån.

## <a name="azure-regions"></a>Azure-regioner

En Azure-region är en uppsättning data Center som distribueras inom en latens-definierad perimeter och är anslutna via ett dedikerat regionalt nätverk med låg latens. Mer information finns i [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions).
