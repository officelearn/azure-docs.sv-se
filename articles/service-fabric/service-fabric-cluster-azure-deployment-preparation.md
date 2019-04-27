---
title: Planera en distribution för Azure Service Fabric-kluster | Microsoft Docs
description: Läs mer om att planera och förbereda för en distribution av Service Fabric-kluster till Azure i produktionsmiljön.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: aljo
ms.openlocfilehash: 0f3a9010805ec1a18490f6f530f60d7a3c763398
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60387867"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Planera och förbereda för distribution av ett kluster

Det är mycket viktigt att planera och förbereda för en Produktionsdistribution för klustret.  Det finns många faktorer att tänka på.  Den här artikeln vägleder dig genom stegen för att förbereda distributionen av klustret.

## <a name="read-the-best-practices-information"></a>Läs informationen om metodtips
Om du vill hantera Azure Service Fabric-program och -kluster har finns det åtgärder som att vi rekommenderar starkt att du utför för att optimera tillförlitligheten för din produktionsmiljö.  Mer information finns i [Metodtips för Service Fabric-program och klustret](service-fabric-best-practices-overview.md).

## <a name="select-the-os-for-the-cluster"></a>Välj operativsystem för klustret
Service Fabric kan för att skapa Service Fabric-kluster på virtuella datorer eller datorer som kör Windows Server eller Linux.  Innan du distribuerar ditt kluster, måste du välja datorns operativsystem:  Windows- eller Linux.  Varje nod (virtuell dator) i klustret kör samma OS kan du blanda inte Windows och Linux-datorer i samma kluster.

## <a name="capacity-planning"></a>Kapacitetsplanering
För alla Produktionsdistribution är kapacitetsplanering ett viktigt steg. Här är några saker att tänka på i samband med den här processen.

* Det inledande antalet nodtyper för klustret 
* Egenskaperna för respektive nodtyp (storlek, antal instanser, primär, internetuppkopplad, antalet virtuella datorer, osv.)
* Klustrets egenskaper för tillförlitlighet och hållbarhet

### <a name="select-the-initial-number-of-node-types"></a>Välj det inledande antalet nodtyper
Du måste först ta reda på vad du skapar klustret som ska användas för. Vilka typer av program som du planerar att distribuera i det här klustret? Har ditt program flera tjänster och behöver någon av dem vara offentligt eller mot internet? Har dina tjänster (som utgör ditt program) för olika infrastrukturbehov, till exempel RAM-minne eller högre CPU-cykler? Service Fabric-kluster kan bestå av fler än en nodtyp: primära nodtypen och en eller flera icke-primär nodtyper. Varje nodtyp mappas till en VM-skalningsuppsättning. Varje nodtyp kan sedan skalas upp eller ned oberoende av de andra, ha olika portar öppna och ha olika kapacitet. [Nodegenskaper och placeringsbegränsningar] [ placementconstraints] kan ställas in för att begränsa vissa tjänster till specifika nodtyper.  Mer information finns i [antal nodtyper som behövs i klustret för att börja med](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with).

### <a name="select-node-properties-for-each-node-type"></a>Välj noden egenskaper för varje nodtyp
Nodtyper definierar VM SKU, antal och egenskaper för de virtuella datorerna i den associerade skalningsuppsättningen.

Den minsta storleken på virtuella datorer för varje nodtyp bestäms av den [hållbarhetsnivå] [ durability] du väljer för nodtypen.

Det minsta antalet virtuella datorer för den primära nodtypen avgörs av det [tillförlitlighetsnivån] [ reliability] du väljer.

Se minimikraven för [primära nodtyper](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance), [tillståndskänsliga arbetsbelastningar på icke-primär nodtyper](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads), och [tillståndslösa arbetsbelastningar på icke-primär nodtyper](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads). 

Fler än det minsta antalet noder ska baseras på antalet repliker för program/tjänster som du vill köra i den här nodtypen.  [Kapacitetsplanering för Service Fabric-program](service-fabric-capacity-planning.md) kan du beräkna de resurser du behöver för att köra dina program. Du kan alltid klustret skala upp eller ned senare för att justera för att ändra arbetsbelastning. 

### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Välj nivåer för hållbarhet och tillförlitlighet för klustret
Hållbarhetsnivån används för att ange de behörigheter som dina virtuella datorer som har med den underliggande Azure-infrastrukturen i systemet. I den primära nodtypen kan privilegiet Service Fabric för att pausa varje virtuell dator på infrastruktur-begäran (till exempel en omstart av virtuell dator, återställa avbildningen av de virtuella datorer eller VM-migrering) som påverkar kvorum kraven för systemtjänster och dina tillståndskänsliga tjänster. I icke-primär nodtyperna kan privilegiet Service Fabric för att pausa alla VM på infrastruktur förfrågningar (till exempel omstart av virtuell dator, återställa avbildningen av de virtuella datorer och VM-migrering) som påverkar kvorum kraven för din tillståndskänsliga tjänster.  För fördelarna med olika nivåer och rekommendationer på vilken nivå du använder och när, se [hållbarhet egenskaper för klustret][durability].

Tillförlitlighetsnivån används för att ange antalet repliker av systemtjänster som du vill köra i det här klustret på den primära nodtypen. Det mer antalet repliker, mer tillförlitliga systemtjänsterna i klustret.  För fördelarna med olika nivåer och rekommendationer på vilken nivå du använder och när, se [tillförlitlighetsegenskaper för klustret][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Aktivera omvänd proxy och/eller DNS
Tjänster som ansluter till varandra i ett kluster Allmänt kan direkt åtkomst till slutpunkterna för andra tjänster eftersom noderna i ett kluster i samma lokala nätverk. Om du vill göra det enklare att ansluta mellan tjänster, tillhandahåller Service Fabric ytterligare tjänster: En [DNS-tjänsten](service-fabric-dnsservice.md) och en [omvänd proxy-tjänst](service-fabric-reverseproxy.md).  Båda tjänsterna kan aktiveras när du distribuerar ett kluster.

Eftersom många tjänster, tjänster i särskilt behållare, kan ha ett befintligt URL-namn, som kan lösa dem med hjälp av standard-DNS-protokollet (i stället för protokollet Namngivningstjänsten) är praktiskt, särskilt i program ”lift and shift”-scenarier. Detta är exakt det DNS-tjänsten. Det kan du mappa DNS-namn till ett tjänstnamn och kan därför matcha IP-adresser för slutpunkt.

Omvänd proxy löser tjänster i klustret som exponerar HTTP-slutpunkter (inklusive HTTPS). Omvänd proxy förenklar anropa andra tjänster genom att tillhandahålla ett specifikt URI-format.  Omvänd proxy hanterar också lösas, ansluta och försök steg som krävs för en tjänst att kommunicera med en annan.

## <a name="prepare-for-disaster-recovery"></a>Förbereda för haveriberedskap
En viktig del av att leverera hög tillgänglighet är att säkerställa att tjänster kan överleva alla olika typer av fel. Detta är särskilt viktigt för fel som är oplanerade och utanför din kontroll. [Förbereda för katastrofåterställning](service-fabric-disaster-recovery.md) beskriver några vanliga feltillstånd som kan vara katastrofer om inte modelleras och hanteras korrekt. Här beskrivs även åtgärder och åtgärder som ska vidtas om en katastrof har inträffat ändå.

## <a name="production-readiness-checklist"></a>Checklista för produktionsberedskap
Är din program- och redo att ta produktionstrafik? Innan du distribuerar ditt kluster till produktion, kör den [produktion beredskap checklista](service-fabric-production-readiness-checklist.md). Se till att program och kluster som kör smidigt genom att arbeta igenom objekten i den här checklistan. Vi rekommenderar att alla dessa objekt som ska kontrolleras innan du påbörjar produktionen.

## <a name="next-steps"></a>Nästa steg
* [Skapa ett Service Fabric-kluster som kör Windows](service-fabric-best-practices-overview.md)
* [Skapa ett Service Fabric-kluster som kör Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster