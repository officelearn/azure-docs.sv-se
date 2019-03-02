---
title: Azure Service Fabric-händelser | Microsoft Docs
description: Läs mer om Service Fabric-händelser som är färdiga för att övervaka Azure Service Fabric-klustret.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: c4ce8e01b1dc819453610f68d044cc268e27eed7
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242759"
---
# <a name="service-fabric-events"></a>Service Fabric-händelser 

Service Fabric-plattformen skriver flera strukturerade händelser för viktiga operativa aktiviteter som sker i klustret. Dessa allt från klusteruppgradering till repliken placering beslut. Varje händelse att Service Fabric exponerar mappas till något av följande enheter i klustret:
* Kluster
* Program
* Tjänst
* Partition
* Replik 
* Container

Se en fullständig lista över händelser som visas av plattformen, [lista av Service Fabric händelser](service-fabric-diagnostics-event-generation-operational.md).

Här följer några exempel på scenarier som du bör se händelser för i klustret. 
* Livscykelhändelser för nod: som kommer upp, gå, skala in och ut, starta om och är aktiverad/inaktiverad, dessa händelser visas där du kan se vad som hände och hjälper dig att identifiera om något är fel med datorn sig själv eller om det uppstod ett API som var anropas via SF att ändra status för en nod.
* Uppgradering av kluster: eftersom klustret uppgraderas (SF version eller konfiguration ändra), ser du initiera, rulla igenom dina uppgradera domäner och slutföra uppgraderingen (eller återställning). 
* Programuppgraderingar: precis som klusteruppgradering, det finns en omfattande uppsättning händelser eftersom uppgraderingen går igenom. Dessa händelser kan vara användbar för att förstå när en uppgradering var schemalagt, det aktuella tillståndet för en uppgradering och den övergripande sekvensen av händelser. Detta är användbart för att söka igen om du vill se vad uppgraderingar har lanserats har eller om en återställning utlöstes.
* Program/tjänst distribution / borttagning: det finns händelser för varje program, en tjänst och en behållare, som skapats eller tagits bort och användbart när skala in eller ut t.ex. öka antalet repliker
* Partition flyttar (omkonfiguration): när en tillståndskänslig partition genomgår en omkonfiguration (en ändring i replikuppsättningen), en händelse loggas. Detta är användbart om du vill förstå hur ofta replikuppsättningen partition ändrar eller växlar vid fel eller spåra vilken nod kördes den primära repliken vid någon tidpunkt i tid.
* Chaos händelser: när du använder Service Fabric [Chaos](service-fabric-controlled-chaos.md) tjänsten måste visas händelser varje gång tjänsten startas eller stoppas eller när det lägger in ett fel i systemet.
* Health-händelser: Service Fabric exponerar hälsohändelser varje gång en varning eller en felrapport för hälsotillstånd har skapats, eller en entitet går tillbaka till det OK hälsotillståndet eller en hälsorapport upphör att gälla. Dessa händelser är användbar för att spåra historiska hälsostatistik för en entitet. 

## <a name="how-to-access-events"></a>Hur du kommer åt händelser

Det finns några olika sätt som Service Fabric-händelser kan nås:
* Händelser loggas via standard-kanaler, till exempel ETW/Windows-händelseloggar och kan visualiseras med alla övervakningsverktyg som har stöd för dessa, till exempel Azure Monitor-loggar. Som standard kluster som skapas i portalen diagnostik aktiveras och har en Windows Azure diagnostics-agenten skickar händelser till Azure table storage, men behöver ändå integrera det med dina log analytics-resursen. Läs mer om hur du konfigurerar den [Azure Diagnostics-agenten](service-fabric-diagnostics-event-aggregation-wad.md) att ändra diagnostikkonfigurationen på klustret att hämta fler loggar eller prestandaräknare och [integration av Azure Monitor-loggar](service-fabric-diagnostics-event-analysis-oms.md)
* EventStore service Rest API: er som gör det möjligt att fråga klustret direkt eller via Service Fabric-klientbiblioteket. Se [fråga EventStore APIs för klusterhändelser](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Nästa steg
* Mer information på övervakning av ditt kluster, [kluster och plattformen](service-fabric-diagnostics-event-generation-infra.md).
* Mer information om tjänsten EventStore - [EventStore tjänstöversikt](service-fabric-diagnostics-eventstore.md)
