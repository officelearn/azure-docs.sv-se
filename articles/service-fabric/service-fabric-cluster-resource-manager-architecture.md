---
title: Resource Manager-arkitektur | Microsoft Docs
description: En översikt över arkitekturen i Service Fabric Cluster Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 6c4421f9-834b-450c-939f-1cb4ff456b9b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: bfbdb05e8d2764d2b878e22d236cae30519da176
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62113981"
---
# <a name="cluster-resource-manager-architecture-overview"></a>Översikt över kluster resource manager-arkitektur
Service Fabric Cluster Resource Manager är en central tjänst som körs i klustret. Den hanterar önskat tillstånd för tjänster i klustret, särskilt med avseende på resursförbrukning och eventuella placeringsregler. 

För att hantera resurserna i ditt kluster, måste Service Fabric Cluster Resource Manager ha flera olika typer av information:

- Vilka tjänster som finns för tillfället
- Varje tjänst aktuella (eller standard) resursförbrukning 
- Återstående klusterkapacitet 
- Kapaciteten för noder i klustret 
- Mängden resurser som används på varje nod

De resurser som används för en viss tjänst kan ändras med tiden och tjänster som vanligtvis bryr dig om mer än en typ av resurs. Mellan olika tjänster, kan det finnas både verkliga fysiska och fysiska resurser som mäts. Tjänster kan spåra fysiska mått som minnes- och förbrukning. Tjänster kan vanligare, bryr dig om logiska mått – till exempel ”WorkQueueDepth” eller ”TotalRequests”. Både logiska och fysiska mått kan användas i samma kluster. Mått kan delas av många tjänster eller vara specifika för en viss tjänst.

## <a name="other-considerations"></a>Annat att tänka på
Ägare och operatörer av klustret kan skilja sig från tjänsten och programmet författare eller är de samma med olika personer hats minst. När du utvecklar programmet vet du några saker om vad som krävs. Du har en uppskattning av de resurser som den kommer att kosta och hur olika tjänster som ska distribueras. Exempelvis måste webbnivå köras på noder som är exponerade för Internet, medan databastjänster inte bör. Ett annat exempel är förmodligen webbtjänsterna begränsade av CPU- och, samtidigt som data nivån tjänster försiktighet mer om minnes- och förbrukning. Men den person som hanterar en live-webbplatser incident för tjänsten i produktion eller som hanterar en uppgradering till tjänsten har ett annat jobb för att göra, och kräver olika verktyg. 

Både klustret och tjänster är dynamiska:

- Antalet noder i klustret växa eller krympa
- Noder i olika storlekar och typer kan komma och gå
- Tjänster kan skapas, tas bort, och ändra deras önskade resursallokeringar och placeringsregler
- Uppgraderingar och andra hanteringsåtgärder kan rulla igenom klustret på programnivå på infrastruktur nivåer
- Fel kan inträffa när som helst.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Cluster resource manager-komponenter och dataflöde
Klusterresurshanteraren har att spåra kraven för varje tjänst och förbrukningen av resurser med varje serviceobjektet i dessa tjänster. Klusterresurshanteraren har två konceptuella delar: agenter som körs på varje nod och en feltolerant tjänst. Agenter på varje nod spåra belastningen rapporter från tjänster, sammanställd dem och regelbundet rapportera dem. Cluster Resource Manager-tjänsten samlar in all information från den lokala agenter och reagerar baserat på dess aktuella konfiguration.

Låt oss titta på diagrammet nedan:

<center>

![För Resursutjämnare][Image1]
</center>

Det finns många ändringar som kan inträffa under körning. Exempelvis kan ändras vi anta mängden resurser som vissa tjänster, vissa tjänster misslyckas, och vissa noder anslutnings- och lämna klustret. Alla ändringar på en nod samman och med jämna mellanrum skickas till Cluster Resource Manager-tjänsten (1,2) där de är igen aggregeras, analyseras och lagras. Några sekunder som tjänsten tittar på ändringarna och avgör om alla åtgärder som krävs (3). Det kan till exempel Observera att vissa tom noder har lagts till i klustret. Därför kan den bestämmer sig för att flytta vissa tjänster till dessa noder. Klusterresurshanteraren kunde Observera också att en särskild nod är överbelastad eller att vissa tjänster har misslyckats eller tagits bort, frigöra resurser någon annanstans.

Nu ska vi titta på diagrammet nedan och se vad som händer härnäst. Anta att Cluster Resource Manager avgör att ändringar är nödvändiga. Den samordnar med andra systemtjänster (särskilt den Redundanshanteraren) för att göra nödvändiga ändringar. Nödvändiga kommandon skickas sedan till lämplig noderna (4). Anta exempelvis att Resource Manager har upptäckt att nod5 var överbelastad och valt så att flytta tjänsten B från nod5 till nod4. I slutet av omkonfiguration (5) i klustret som ser ut så här:

<center>

![För Resursutjämnare][Image2]
</center>

## <a name="next-steps"></a>Nästa steg
- Klusterresurshanteraren har många alternativ för att beskriva klustret. Om du vill veta mer om dem, Kolla in den här artikeln på [som beskriver ett Service Fabric-kluster](./service-fabric-cluster-resource-manager-cluster-description.md)
- Cluster Resource Manager primära uppgifter ombalansering klustret och tillämpa regler för placering. Läs mer om hur du konfigurerar dessa beteenden [belastningsutjämning Service Fabric-klustret](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
