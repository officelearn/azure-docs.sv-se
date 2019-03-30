---
title: 'Möjligheten att testa: Tjänster | Microsoft Docs'
description: Tjänst-till-tjänst-kommunikation är en kritisk integrering av ett Service Fabric-program. Den här artikeln beskrivs överväganden och tekniker för testning.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 017557df-fb59-4e4a-a65d-2732f29255b8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 529c8d74b6e0a63a7969f31d5b5e8073ecb79411
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665754"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Service Fabric-testningsscenarier: Tjänstkommunikation
Mikrotjänster och tjänst-orienterade arkitekturstilar angrepp naturligt i Azure Service Fabric. I dessa typer av distribuerade arkitekturer består komponentbaserade mikrotjänstprogram vanligtvis av flera tjänster som behöver kommunicera med varandra. I och med de enklaste fallen du generellt sett ha minst en tillståndslös webbtjänst och en tillståndskänslig tjänst för lagring som behöver kommunicera.

Tjänst-till-tjänst-kommunikation är en kritisk integrering för ett program, eftersom varje tjänst Exponerar en fjärr-API till andra tjänster. Arbeta med en uppsättning API-gränser som innebär att i/o Allmänt kräver viss precision med mycket testning och validering.

Det finns flera saker du måste ta när dessa gränser för tjänsten wired tillsammans i ett distribuerat system:

* *Transport protocol*. Ska du använda HTTP för ökad samverkan eller ett anpassat binära protokoll för maximalt dataflöde?
* *Felhantering*. Hur kommer permanent och tillfälliga fel hanteras? Vad händer när en tjänst som flyttas till en annan nod?
* *Tidsgränser och svarstid*. I multitiered program, hur varje tjänstnivå hanterar svarstid via stacken och för användaren?

Om du använder en av de komponenterna för inbyggd tjänst-kommunikation som tillhandahålls av Service Fabric eller om du skapar dina egna, tester samverkan mellan dina tjänster är avgörande för att säkerställa återhämtning i ditt program.

## <a name="prepare-for-services-to-move"></a>Förbereda för tjänster för att flytta
Instanser av tjänsten kan flytta runt över tid. Detta gäller särskilt när de är konfigurerade med inläsningsmåtten för anpassad optimal Resursanvändning av nätverksbelastning. Service Fabric flyttar dina service-instanser för att maximera sin tillgänglighet även under uppgraderingar, växling vid fel, skalbar och andra situationer som uppstår under livslängden för ett distribuerat system.

Eftersom tjänster runt i klustret, ska klienter och andra tjänster förberedas för att hantera två scenarier när de kommunicerar med en tjänst:

* Service-instans eller partition repliken har flyttats sedan den senaste gången som du har talat med den. Det här är en normal del av en tjänst livscykel och det förväntas ske under livslängden för ditt program.
* Service-instans eller partition repliken håller på att flytta. Även om växling av en tjänst från en nod till en annan sker snabbt i Service Fabric, kan det uppstå en fördröjning i tillgänglighet om kommunikationskomponenten i din tjänst går långsamt att starta.

Hantering av dessa scenarier på ett smidigt sätt är viktigt för ett system för smooth körs. Om du vill göra det, Tänk på att:

* Varje tjänst som kan anslutas till har en *adress* som den lyssnar på (till exempel http- eller WebSockets). När det flyttar en tjänstinstans eller partition, ändrar dess adress-slutpunkten. (Den flyttas till en annan nod med en annan IP-adress.) Om du använder inbyggd kommunikation-komponenter, hanterar de igen lösa postadresser åt dig.
* Det kan finnas en tillfällig ökning service svarstid som tjänsten instans startar upp dess lyssnare igen. Detta beror på hur snabbt tjänsten öppnas lyssnaren när tjänstinstansen har flyttats.
* Befintliga anslutningar måste du stänga och öppna när tjänsten öppnas på en ny nod. En korrekt nodavstängning eller omstart kan du tid för befintliga anslutningar stängs utan problem.

### <a name="test-it-move-service-instances"></a>Testa den: Flytta service-instanser
Med Service Fabric testning kan skapa du ett Testscenario för att testa dessa situationer på olika sätt:

1. Flytta en tillståndskänslig tjänst primära repliken.
   
    Den primära repliken av en tillståndskänslig tjänstpartition kan flyttas till många olika orsaker. Används för att rikta den primära repliken av en specifik partition att se hur dina tjänster reagera på flytten på ett mycket kontrollerat sätt.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Stoppa en nod.
   
    När en nod har stoppats, flyttar Service Fabric alla instanser av tjänsten eller partitioner som fanns på noden till en av de andra tillgängliga noderna i klustret. Används för att testa en situation där en nod är går förlorade från klustret och alla instanser av tjänsten och repliker på den nod som har att flytta.
   
    Du kan stoppa en nod med hjälp av PowerShell **Stop-ServiceFabricNode** cmdlet:
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Upprätthålla tjänstens tillgänglighet
Service Fabric är utformad som en plattform för att ge hög tillgänglighet för dina tjänster. Men i extrema fall underliggande infrastruktur kan fortfarande ha otillgängliga. Det är viktigt att testa dessa scenarier för.

Tillståndskänsliga tjänster använda en kvorum-dator för att replikera statusen för hög tillgänglighet. Det innebär att ett kvorum av repliker måste vara tillgängliga för att utföra skrivåtgärder. Ett kvorum av repliker kanske inte tillgänglig i sällsynta fall, till exempel ett omfattande maskinvarufel. I dessa fall kan du inte kommer att kunna utföra skrivåtgärder, men du kommer fortfarande att kunna utföra läsåtgärder.

### <a name="test-it-write-operation-unavailability"></a>Testa den: Skriva åtgärden otillgänglighet
Med möjligheten att testa verktyg i Service Fabric kan du mata in ett fel som åstadkommer förlorar kvorum som ett test. Även om ett sådant scenario är sällsynt, är det viktigt att klienter och tjänster som är beroende av en tillståndskänslig tjänst är beredd att hantera situationer där denne inte fatta skrivförfrågningar till den. Det är också viktigt att den tillståndskänsliga tjänsten själva är medveten om den här möjligheten och kan smidigt kommunicera till anropare.

Du kan ge upphov till förlorar kvorum med hjälp av PowerShell **Invoke-ServiceFabricPartitionQuorumLoss** cmdlet:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

I det här exemplet anger vi `QuorumLossMode` till `QuorumReplicas` ange att vi vill framkalla förlorar kvorum utan att alla repliker. På så sätt kan läsåtgärder är fortfarande möjliga. Om du vill testa ett scenario där en hel partition är tillgänglig, du kan ange den här växeln till `AllReplicas`.

## <a name="next-steps"></a>Nästa steg
[Mer information om testningsåtgärder](service-fabric-testability-actions.md)

[Mer information om testningsscenarier](service-fabric-testability-scenarios.md)

