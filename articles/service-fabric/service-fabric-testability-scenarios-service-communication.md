---
title: 'Möjlighet att testa: Tjänsten kommunikation | Microsoft Docs'
description: Service-to-service-kommunikation är en kritisk integrering av ett Service Fabric-program. Den här artikeln beskrivs överväganden vid utformning och tester tekniker.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 017557df-fb59-4e4a-a65d-2732f29255b8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: e3ea537d310d49c934cf6789184f090791cf16a4
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211233"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Service Fabric möjlighet att testa scenarier: tjänsten kommunikation
Mikrotjänster och tjänstorienterad arkitektur formatmallar ytan naturligt i Azure Service Fabric. I dessa typer av distribuerade arkitekturer består komponentbaserade mikrotjänster program vanligtvis av flera tjänster som behöver kommunicera med varandra. I även de enklaste fall kan har du vanligtvis minst en tillståndslös webbtjänst och en tillståndskänslig data storage-tjänst som behöver kommunicera.

Service-to-service-kommunikation är en kritisk integrering av ett program, eftersom varje tjänst Exponerar en fjärr-API till andra tjänster. Arbeta med en uppsättning API-gränser som innebär att i/o vanligtvis kräver viss precision med mycket testning och validering.

Det finns många saker du måste ta när dessa gränser för tjänsten wired tillsammans i ett distribuerat system:

* *Transport-protokollet*. Ska du använda HTTP för ökad samverkan eller anpassade binära protokoll för maximalt dataflöde?
* *Felhantering*. Hur permanent och tillfälliga fel hanteras? Vad händer när en tjänst som flyttar till en annan nod?
* *Tidsgränser och fördröjning*. I multitiered program, hur varje tjänstnivå hanterar latens via stacken och för användaren?

Om du använder en inbyggd tjänst kommunikation komponenter som tillhandahålls av Service Fabric eller om du skapar egna, tester samverkan mellan dina tjänster är kritiska för återhämtning i ditt program.

## <a name="prepare-for-services-to-move"></a>Förbereda för services för att flytta
Instanser av tjänsten kan flytta över tid. Detta gäller särskilt när de är konfigurerade med belastningen anpassad skräddarsydda optimal Resursanvändning belastningsutjämning. Service Fabric flyttar instanser av tjänsten för att maximera deras tillgänglighet även under uppgraderingar, redundans, skalbara och andra situationer som sker över livslängden för ett distribuerat system.

När tjänster flyttar i klustret, ska klienterna och andra tjänster förberedas för att hantera två scenarier när de kommunicerar med en tjänst:

* Service-instans eller partition repliken har flyttats sedan förra gången du talade vi till den. Detta är en normal del av en livscykel för tjänsten och den bör förväntas under livslängden för ditt program.
* Service-instans eller partition repliken håller på att flytta. Även om växling vid fel på en tjänst från en nod till en annan uppstår mycket snabbt i Service Fabric kan finnas det en fördröjning i tillgänglighet om kommunikationskomponenten av tjänsten tar lång tid att starta.

Hantering av dessa scenarier smidigt är viktigt för ett system för smooth körs. Om du vill göra det måste du tänka på:

* Varje tjänst som kan anslutas till har en *adress* att den lyssnar på (till exempel http- eller WebSockets). När du flyttar en tjänstinstans eller partition, ändrar sin adress-slutpunkt. (Den flyttas till en annan nod med en annan IP-adress.) Om du använder inbyggd kommunikationskomponenter hanterar de nytt lösa postadresser åt dig.
* Det kan finnas en tillfällig ökning i tjänsten svarstid som startar tjänsten instans av dess lyssnare igen. Detta beror på hur snabbt tjänsten öppnar lyssnaren när tjänstinstansen flyttas.
* Alla befintliga anslutningar måste du stänga och öppna när tjänsten öppnas på en ny nod. En korrekt nodavstängning eller omstart ger dig tid för befintliga anslutningar stängs avslutas.

### <a name="test-it-move-service-instances"></a>Testa den: flytta instanser av tjänsten
Med hjälp av Service Fabric datatillgång verktyg kan skapa du ett Testscenario om du vill testa dessa situationer på olika sätt:

1. Flytta en tillståndskänslig service primära repliken.
   
    Den primära repliken av en tillståndskänslig service partition kan flyttas av olika orsaker. Används för att rikta den primära repliken av en specifik partition för att se hur dina tjänster reagerar på flytten på ett mycket kontrollerat sätt.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Stoppa en nod.
   
    När en nod har stoppats, flyttar Service Fabric alla instanser av tjänsten eller partitioner som fanns på den noden till en av de andra tillgängliga noderna i klustret. Används för att testa en situation där en nod tappas bort från klustret och alla tjänstinstanser och repliker på noden måste flytta.
   
    Du kan stoppa en nod med hjälp av PowerShell **stoppa ServiceFabricNode** cmdlet:
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Underhåll tjänsttillgänglighet för
Service Fabric är utformad att ge hög tillgänglighet för dina tjänster som en plattform. Men i extrema fall underliggande infrastruktur kan fortfarande ha inte finns. Det är viktigt att testa för dessa scenarier för.

Tillståndskänsliga tjänster använder en kvorum-dator för att replikera statusen för hög tillgänglighet. Det innebär att ett kvorum av repliker måste vara tillgängliga för att utföra skrivåtgärder. Ett kvorum av repliker kanske inte tillgänglig i sällsynta fall, till exempel ett omfattande maskinvarufel. Du kommer inte att kunna utföra skrivåtgärder i dessa fall, men du kommer fortfarande att kunna utföra läsåtgärder.

### <a name="test-it-write-operation-unavailability"></a>Testa den: skriva åtgärden otillgänglighet
Med hjälp av verktygen datatillgång i Service Fabric du mata in ett fel som startar förlorar kvorum som ett test. Även om ett sådant scenario är sällsynt, är det viktigt att klienter och tjänster som är beroende av en tillståndskänslig service är beredd att hantera situationer där denne inte fatta skrivåtgärder till den. Det är också viktigt att tillståndskänslig själva tjänsten känner till denna möjlighet och kan smidigt kommunicera till anropare.

Du kan ge upphov till förlorar kvorum med hjälp av PowerShell **Invoke-ServiceFabricPartitionQuorumLoss** cmdlet:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

I det här exemplet anger vi `QuorumLossMode` till `QuorumReplicas` att indikera att vi vill framkalla förlorar kvorum utan att stoppa alla repliker. Det här sättet läsåtgärder är fortfarande möjligt. Om du vill testa ett scenario där en hel partition är tillgänglig, du kan ange den här växeln till `AllReplicas`.

## <a name="next-steps"></a>Nästa steg
[Mer information om datatillgång åtgärder](service-fabric-testability-actions.md)

[Mer information om möjlighet att testa scenarier](service-fabric-testability-scenarios.md)

