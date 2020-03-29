---
title: Azure-infrastrukturhändelser för tjänsten
description: Lär dig mer om service fabric-händelser som tillhandahålls direkt för att hjälpa dig att övervaka ditt Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 638b650e485ad3e83bd6021639a7e55b540d9cdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451732"
---
# <a name="service-fabric-events"></a>Service Fabric-händelser 

Service Fabric-plattformen skriver flera strukturerade händelser för viktiga operativa aktiviteter som händer inom klustret. Dessa sträcker sig från klusteruppgraderingar till replikplaceringsbeslut. Varje händelse som Service Fabric exponerar kartor till en av följande entiteter i klustret:
* Kluster
* Program
* Tjänst
* Partition
* Replik 
* Container

Om du vill se en fullständig lista över händelser som exponeras av plattformen - [Lista över service fabric-händelser](service-fabric-diagnostics-event-generation-operational.md).

Här är några exempel på scenarier som du bör se händelser för i klustret. 
* Nodlivscykelhändelser: när noder kommer upp, går ner, skalar in/ut, startar om och aktiveras/inaktiveras, kommer dessa händelser att exponeras som visar vad som hände och hjälper dig att identifiera om det är något fel på själva datorn eller om det fanns ett API som anropades via SF för att ändra status för en nod.
* Klusteruppgradering: När klustret uppgraderas (SF-version eller konfigurationsändring) ser du uppgraderingsutitieringen, rullar genom var och en av dina uppgraderingsdomäner och slutför (eller återställning). 
* Programuppgraderingar: Precis som klusteruppgraderingar finns det en omfattande uppsättning händelser när uppgraderingen rullar igenom. Dessa händelser kan vara användbara för att förstå när en uppgradering har schemalagts, det aktuella tillståndet för en uppgradering och den övergripande händelsesekvensen. Detta är användbart för att se tillbaka för att se vilka uppgraderingar som har distribuerats eller om en återställning utlöstes.
* Distribution/borttagning av program/tjänst: det finns händelser för varje program, tjänst och behållare, som skapas eller tas bort och användbart när du skalar in eller ut t.ex.
* Partitionen flyttas (omkonfigurering): när en tillståndskänslig partition går igenom en omkonfiguration (en ändring i replikuppsättningen) loggas en händelse. Detta är användbart om du försöker förstå hur ofta partitionsreplikuppsättningen ändras eller växlas över, eller spåra vilken nod som körde din primära replik vid någon tidpunkt.
* Kaoshändelser: När du använder Service Fabric's [Chaos-tjänsten](service-fabric-controlled-chaos.md) visas händelser varje gång tjänsten startas eller stoppas, eller när den injicerar ett fel i systemet.
* Hälsohändelser: Service Fabric exponerar hälsohändelser varje gång en varning eller en felhälsorapport skapas, eller en entitet återgår till ett OK-hälsotillstånd eller en hälsorapport upphör att gälla. Dessa händelser är till stor hjälp för att spåra historisk hälsostatistik för en entitet. 

## <a name="how-to-access-events"></a>Så här kommer du åt händelser

Det finns några olika sätt på vilka Service Fabric-händelser kan nås:
* Händelserna loggas via standardkanaler som ETW/Windows-händelseloggar och kan visualiseras av alla övervakningsverktyg som stöder dessa, till exempel Azure Monitor-loggar. Som standard har kluster som skapats i portalen aktiverat diagnostik och har Windows Azure-diagnostikagenten som skickar händelserna till Azure-tabelllagring, men du måste fortfarande integrera detta med din logganalysresurs. Läs mer om hur du konfigurerar [Azure Diagnostics-agenten](service-fabric-diagnostics-event-aggregation-wad.md) för att ändra diagnostikkonfigurationen för ditt kluster för att hämta fler loggar eller prestandaräknare och [Azure Monitor-loggningsintegreringen](service-fabric-diagnostics-event-analysis-oms.md)
* EventStore-tjänstens Rest API:er som gör att du kan fråga klustret direkt eller via Service Fabric-klientbiblioteket. Se [Frågehändelsestore API:er för klusterhändelser](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Nästa steg
* Mer information om övervakning av klustret - [Övervakning av klustret och plattformen](service-fabric-diagnostics-event-generation-infra.md).
* Läs mer om EventStore-tjänsten - [Serviceöversikt för EventStore](service-fabric-diagnostics-eventstore.md)
