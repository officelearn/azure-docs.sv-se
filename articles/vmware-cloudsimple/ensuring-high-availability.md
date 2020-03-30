---
title: Se till att programmet är hög tillgänglighet när du kör i VMware på Azure
description: Beskriver CloudSimple funktioner med hög tillgänglighet för att hantera vanliga scenarier för programfel för program som körs i ett CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a3eed033ba6a1a6f9237116a53ec7751ae906fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025358"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Se till att programmet är hög tillgänglighet när du kör i VMware på Azure

CloudSimple-lösningen ger hög tillgänglighet för dina program som körs på VMware i Azure-miljön. I följande tabell visas felscenarier och tillhörande funktioner med hög tillgänglighet.

| Felscenario | Programskyddad? | Funktionen Plattform HA | VMware HA-funktionen | Azure HA-funktionen |
------------ | ------------- | ------------ | ------------ | ------------- |
| Diskfel | JA | Snabb ersättning av misslyckad nod | [Om principen om vSAN-standardlagring](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| Fläkt fel | JA | Redundanta fläktar, snabb ersättning av misslyckad nod |  |  |
| NIC-fel | JA | Redundant nätverkskort, snabb ersättning av misslyckad nod
| Strömavbrott för värden | JA | Redundant strömförsörjning |  |  |
| ESXi-värdfel | JA | snabb ersättning av misslyckad nod | [VMware vSphere hög tillgänglighet](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| Fel på den virtuella datorn | JA | [Lastbalanserare](load-balancers.md)  | [VMware vSphere hög tillgänglighet](https://www.vmware.com/products/vsphere/high-availability.html) | Azure Load Balancer för tillståndslösa virtuella datorer med VMware |
| Fel i lövväxlingsporten | JA | Redundant nätverkskort |  |  |
| Fel vid lövväxling | JA | Redundanta bladväxlar |  |  |
| Rack fel | JA | Placeringsgrupper |  |  |
| Nätverksanslutning till lokal dc | JA  | Redundanta nätverkstjänster |  | Redundanta ER-kretsar |
| Nätverksanslutning till Azure | JA | |  | Redundanta ER-kretsar |
| Datacenterfel | JA |  |  | Tillgänglighetszoner |
| Regionalt misslyckande | JA  |  |  | Azure-regioner |

Azure VMware Solution by CloudSimple tillhandahåller följande funktioner med hög tillgänglighet.

## <a name="fast-replacement-of-failed-node"></a>Snabb ersättning av misslyckad nod

CloudSimple-styrplanprogramvaran övervakar kontinuerligt hälsan hos VMware-kluster och identifierar när en ESXi-nod misslyckas. Den lägger sedan automatiskt till en ny ESXi-värd i det berörda VMware-klustret från sin pool med lättillgängliga noder och tar den misslyckade noden ut ur klustret. Den här funktionen säkerställer att den lediga kapaciteten i VMware-klustret återställs snabbt så att klustrets återhämtning som tillhandahålls av vSAN och VMware HA återställs.

## <a name="placement-groups"></a>Placeringsgrupper

En användare som skapar ett privat moln kan välja en Azure-region och en placeringsgrupp inom den valda regionen. En placeringsgrupp är en uppsättning noder som är spridda över flera rack men inom samma ryggradsnätverkssegment. Noder inom samma placeringsgrupp kan nå varandra med högst två extra switchhopp. En placeringsgrupp finns alltid inom en enda Azure-tillgänglighetszon och sträcker sig över flera rack. CloudSimple-kontrollplanet distribuerar noder i ett privat moln över flera rack baserat på bästa möjliga ansträngning. Noder i olika placeringsgrupper garanteras placeras i olika rack.

## <a name="availability-zones"></a>Tillgänglighetszoner

Tillgänglighetszoner är ett erbjudande med hög tillgänglighet som skyddar dina program och data från datacenterfel. Tillgänglighetszoner är särskilda fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. Varje region har en tillgänglighetszon. Mer information finns [i Vad är tillgänglighetszoner i Azure?](../availability-zones/az-overview.md).

## <a name="redundant-azure-expressroute-circuits"></a>Redundanta Azure ExpressRoute-kretsar

Datacenteranslutning till Azure vNet med ExpressRoute har redundanta kretsar för att tillhandahålla nätverksanslutningslänk med hög tillgänglighet.

## <a name="redundant-networking-services"></a>Redundanta nätverkstjänster

Alla CloudSimple-nätverkstjänster för det privata molnet (inklusive VLAN, brandvägg, offentliga IP-adresser, Internet och VPN) är utformade för att vara mycket tillgängliga och kan stödja service-SLA.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Azure Layer 7 belastningsutjämnare för tillståndslösa virtuella datorer med VMware

Användare kan placera en Azure Layer 7 Load Balancer framför de tillståndslösa virtuella datorer på webbnivå som körs i VMware-miljön för att uppnå hög tillgänglighet för webbnivån.

## <a name="azure-regions"></a>Azure-regioner

En Azure-region är en uppsättning datacenter som distribueras inom en svarstidsdefinierad perimeter och ansluten via ett dedikerat regionalt nätverk med låg latens. Mer information finns i [Azure Regions](https://azure.microsoft.com/global-infrastructure/regions).
