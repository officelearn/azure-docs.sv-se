---
title: Azure Service Fabric-händelser | Microsoft Docs
description: Läs mer om Service Fabric-händelser som är färdiga för att övervaka Azure Service Fabric-klustret.
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
ms.openlocfilehash: ca63d67f6d7c19b4ca6928c4cc0f9ccb06eace2b
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49402989"
---
# <a name="service-fabric-events"></a>Service Fabric-händelser 

Service Fabric-plattformen skriver flera strukturerade händelser för viktiga driftaktiviteter som händer i ditt kluster. Dessa allt från klusteruppgradering till repliken placering beslut. Varje händelse att Service Fabric exponerar mappas till något av följande enheter i klustret:
* Kluster
* Program
* Tjänst
* Partition
* Replik 
* Container

Se en fullständig lista över händelser som visas av plattformen, [lista av Service Fabric händelser](service-fabric-diagnostics-event-generation-operational.md).

Här följer några exempel på viktiga scenarier som du bör se händelser för i klustret. 
1. Livscykelhändelser för nod: noder kommer upp, stängs, är aktiverad/inaktiverad av eller startas om, händelser visas där du kan se vad som hände och hjälper dig att identifiera om det är något fel med datorn sig själv eller om det uppstod ett API som anropades via SF till Ändra status för en nod.
1. Uppgradering av kluster: eftersom klustret uppgraderas (SF version eller konfiguration ändra), ser du initiera, rulla igenom dina ud och slutföra uppgraderingen (eller återställning). 
1. Programuppgraderingar: uppgraderas på samma sätt ska ingå i klustret, det finns en omfattande uppsättning händelser eftersom uppgraderingen går igenom. Dessa händelser kan vara användbar för att förstå när en uppgradering var schemalagt, det aktuella tillståndet för en uppgradering och den övergripande sekvensen av händelser. Detta är användbart för att söka igen för att se vad uppgraderingar har lanserats har.
1. Program/tjänst distribution / borttagning: det finns händelser för varje program, tjänst och behållare som skapas eller tas bort.
1. Partition flyttar (omkonfiguration): när en tillståndskänslig partition genomgår en omkonfiguration (en ändring i replikuppsättningen), en händelse loggas. Detta är användbart om du vill förstå hur ofta replikuppsättningen partition ändras eller spåra vilken nod kördes den primära repliken vid någon tidpunkt i tid.
1. Chaos händelser: när du använder Service Fabric [Chaos](service-fabric-controlled-chaos.md) tjänsten måste visas händelser varje gång tjänsten startas eller stoppas eller när det lägger in ett fel i systemet.
1. Hälsohändelser: Service Fabric exponerar hälsohändelser varje gång en varning eller en felrapport för hälsotillstånd har skapats, eller en entitet går tillbaka till det OK hälsotillståndet eller en hälsorapport upphör att gälla. Dessa händelser är användbar för att spåra historiska hälsostatistik för en entitet. 

## <a name="how-to-access-events"></a>Hur du kommer åt händelser

Det finns några olika sätt som Service Fabric-händelser kan nås:
* via den operativa kanalen. Dessa kan samlas in via Azure Diagnostics-tillägget och skickas i en lagringstabell för förbrukning eller samlats in till ett verktyg som Azure Log Analytics. När ”diagnostik” är aktiverat för ett kluster, Azure Diagnostics-agenten har distribuerats i ditt kluster och är som standard konfigurerad för att läsa i loggar från den operativa kanalen. Läs mer om hur du konfigurerar den [Azure Diagnostics-agenten](service-fabric-diagnostics-event-aggregation-wad.md) att ändra diagnostikkonfigurationen på klustret att hämta fler loggar eller prestandaräknare. 
* via EventStore-tjänstens Rest API: er som gör det möjligt att fråga klustret direkt eller via Service Fabric-klientbiblioteket. Se [fråga EventStore APIs för klusterhändelser](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Nästa steg
* Mer information på övervakning av ditt kluster, [kluster och plattformen](service-fabric-diagnostics-event-generation-infra.md).
* Mer information om tjänsten EventStore - [EventStore tjänstöversikt](service-fabric-diagnostics-eventstore.md)
