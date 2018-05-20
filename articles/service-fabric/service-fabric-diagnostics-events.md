---
title: Azure Service Fabric händelser | Microsoft Docs
description: Läs mer om Service Fabric händelserna tillhandahållna out of box som hjälper dig att övervaka Azure Service Fabric-klustret.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: b9372c806eab1b0ca69ba078d972b076c8a7d6f6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-events"></a>Service Fabric-händelser 

Service Fabric-plattformen skriver flera strukturerade händelser för viktiga operativa åtgärder som händer i klustret. Dessa mellan klusteruppgradering och repliken placering beslut. Varje händelse att Service Fabric exponerar mappas till något av följande enheter i klustret:
* Kluster
* Program
* Tjänst
* Partition
* Replik 
* Behållare

Se en fullständig lista över händelser som exponeras av plattform - [lista av Service Fabric händelser](service-fabric-diagnostics-event-generation-operational.md).

Här följer några exempel på viktiga scenarier som du bör se händelser för i klustret. 
1. Noden Livscykelhändelser: noder tas upp är kraschar, aktiveras/inaktiveras eller startas om, händelser visas visar vad som hände och hjälper dig att identifiera om det är något fel med själva maskinen eller om det fanns en API som anropades via sa att Ändra status för en nod.
1. Klustret uppgraderingen: som klustret uppgraderas (SA version eller konfiguration ändra), ser du initiera rulla igenom din UDs och slutföra uppgraderingen (eller återställning). 
1. Programuppgraderingar: på samma sätt för klustret uppgraderas, har en omfattande uppsättning händelser under uppgraderingen samlar via. Dessa händelser kan vara användbar för att förstå när uppgradering schemalades, aktuell status för en uppgradering och övergripande sekvens av händelser. Detta är användbart för att söka bakåt för att se vad uppgraderingar har återställts har.
1. Program/tjänst distribution / borttagning: det finns händelser för varje program, tjänst och behållare som skapas eller tas bort.
1. Partitionen flyttar (omkonfiguration): när en tillståndskänslig partition genomgår en omkonfiguration (en ändring i replikuppsättningen), en händelse loggas. Detta är användbart om du vill förstå hur ofta replikuppsättningen partition ändras eller spåra vilken nod kördes den primära repliken när som helst i tid.
1. Chaos händelser: när du använder Service Fabric [Chaos](service-fabric-controlled-chaos.md) tjänsten visas händelser när tjänsten startas eller stoppas eller när den lägger in ett fel i systemet.
1. Hälsotillstånd händelser: Service Fabric exponerar hälsa händelser varje gång en varning eller en felrapport hälsa skapas en entitet går tillbaka till ett OK hälsotillstånd eller en hälsorapport upphör att gälla. Dessa händelser är mycket användbart för att spåra historiska hälsostatistik för en entitet. 

## <a name="how-to-access-events"></a>Hur du kommer åt händelser

Det finns några olika sätt som Service Fabric-händelser kan nås:
* via den operativa kanalen. Dessa kan samlas in via tillägg för Azure-diagnostik och skickas till en tabell för lagring för användning eller införandet till ett verktyg som OMS logganalys. När ”-diagnostik är aktiverat för ett kluster, Azure-diagnostik-agent har distribuerats på klustret och är som standard konfigurerad för att läsa i loggar från den operativa kanalen. Läs mer om hur du konfigurerar den [Azure Diagnostics agent](service-fabric-diagnostics-event-aggregation-wad.md) att ändra konfigurationen för diagnostik av klustret för att hämta mer loggar eller prestandaräknare. 
* via tjänsten EventStore Rest API: er som gör det möjligt att fråga klustret direkt eller via Service Fabric-klientbiblioteket. Se [frågan EventStore APIs för klusterhändelser](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Nästa steg
* Mer information övervakning på ditt kluster - [övervakning av kluster och plattform](service-fabric-diagnostics-event-generation-infra.md).
* Mer information om tjänsten EventStore - [EventStore översikt över service](service-fabric-diagnostics-eventstore.md)
