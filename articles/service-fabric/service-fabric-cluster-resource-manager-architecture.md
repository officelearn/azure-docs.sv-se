---
title: Arkitektur för hanteraren för filserverresurser | Microsoft Docs
description: En översikt över arkitekturen i Service Fabric klustret Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 6c4421f9-834b-450c-939f-1cb4ff456b9b
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 48da92be0eef1154b490fb4829363598d6d66569
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211437"
---
# <a name="cluster-resource-manager-architecture-overview"></a>Översikt över kluster resource manager-arkitektur
Service Fabric klustret Resource Manager är en central tjänst som körs i klustret. Den hanterar tillståndet som tjänster i klustret, särskilt i form av resursförbrukning och regler för placering. 

För att hantera resurser i klustret, har Service Fabric klustret Resource Manager flera typer av information:

- Tjänster som finns för närvarande
- Varje tjänst är aktuella (eller standard) förbrukning av nätverksresurser 
- Återstående klustret kapacitet 
- Kapaciteten för noder i klustret 
- Mängden resurser som används på varje nod

De resurser som används för en viss tjänst kan ändras med tiden och tjänster är vanligtvis intresserad av mer än en typ av resurs. Över olika tjänster kan det finnas både verkliga fysiska och fysiska resurser som mäts. Tjänster kan spåra fysiska mått som minne och användning. Tjänster kan vanligare, intresserad logiska mått - ”WorkQueueDepth” eller ”TotalRequests”. Både logiska och fysiska mått kan användas i samma kluster. Mått kan delas mellan många tjänster eller vara specifika för en viss tjänst.

## <a name="other-considerations"></a>Andra överväganden
Ägare och operatorer i klustret kan skilja sig från tjänster och tillämpningsprogram författarna eller minst är de samma med olika personer tak. När du utvecklar programmet vet du några saker om vad som krävs. Du har en uppskattning av de resurser som den förbrukar och hur olika tjänster ska distribueras. Till exempel måste webbnivån köras på noder som exponeras för Internet, medan database-tjänster inte bör. Ett annat exempel är begränsad webbtjänsterna förmodligen av CPU- och, när data nivå services försiktighet mer om minne och användning. Den person som hanterar en live-plats incident för tjänsten i produktion eller som hanterar en uppgradering till tjänsten har dock ett annat jobb att göra, och kräver olika verktyg. 

Är dynamiska både klustret och tjänster:

- Antalet noder i klustret kan växa eller krympa
- Noder i olika storlekar och typer kan hämtas och gå
- Tjänster kan skapas, tas bort, och ändra resursallokeringar och regler för placering
- Uppgraderingar eller andra hanteringsåtgärder kan rulla genom klustret när programmet på infrastruktur nivåer
- Fel kan inträffa när som helst.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Klustret resource manager-komponenter och dataflöde
Klustret Resource Manager har att spåra kraven för varje tjänst och förbrukningen av resurser med varje serviceobjektet i dessa tjänster. Klustret Resource Manager har två konceptuella delar: agenter som körs på varje nod och en feltolerant tjänst. Agenterna på varje nod spåra belastningen rapporter från tjänster, sammanställd dem och rapporterar dem med jämna mellanrum. Klustret Resource Manager-tjänsten samlar in all information från lokala agenter och reagerar baserat på dess aktuella konfiguration.

Nu ska vi titta på följande diagram:

<center>
![Resursen belastningsutjämnaren arkitektur][Image1]
</center>

Det finns många ändringar som kan inträffa under körning. Till exempel ändras Låt oss säga mängden resurser som använder vissa tjänster vissa tjänster misslyckas och vissa noder koppling och lämna klustret. Alla ändringar på en nod sammanställs och skickas regelbundet till klustret Resource Manager-tjänsten (1,2) där de är samman igen, analyseras och lagras. Några sekunder som tjänsten tittar på ändringarna och avgör om åtgärder som krävs (3). Det kan t.ex, Observera att vissa tom noder har lagts till i klustret. Därför beslutar att flytta vissa tjänster till de noderna. Resurshanteraren för klustret kunde Observera också att en viss nod är överbelastad eller att vissa tjänster har misslyckades eller tagits bort, frigöra resurser någon annanstans.

Nu ska vi titta på följande diagram och se vad som händer nästa. Anta att klustret Resource Manager avgör att ändringar behövs. Den samordnar med andra systemtjänster (särskilt Failover Manager) för att göra nödvändiga ändringar. Nödvändiga kommandon skickas sedan till lämplig noder (4). Anta exempelvis att Resource Manager har upptäckt att nod5 var överbelastad och därför valt att flytta tjänsten B från nod5 till nod4. I slutet av omkonfiguration (5) i klustret som ser ut så här:

<center>
![Resursen belastningsutjämnaren arkitektur][Image2]
</center>

## <a name="next-steps"></a>Nästa steg
- Klustret Resource Manager har många alternativ för att beskriva klustret. Kolla in den här artikeln om du vill veta mer om dem på [som beskriver ett Service Fabric-kluster](./service-fabric-cluster-resource-manager-cluster-description.md)
- Kluster-resurshanteraren primära uppgifter ombalansering klustret och genomdriva regler för placering. Mer information om hur du konfigurerar dessa beteenden finns [NLB Service Fabric-kluster](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
