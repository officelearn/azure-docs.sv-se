---
title: 'Testbarhet: Servicekommunikation'
description: Service-to-service-kommunikation är en kritisk integrationspunkt för ett Service Fabric-program. I den här artikeln beskrivs designöverväganden och testtekniker.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 87b922cb9655588a22c739d26c9ce9e49d35781a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465558"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Testbarhetsscenarier för tjänst fabricering: Servicekommunikation
Mikrotjänster och serviceorienterade arkitektoniska stilar finns naturligt i Azure Service Fabric. I dessa typer av distribuerade arkitekturer består komponentiserade mikrotjänstprogram vanligtvis av flera tjänster som behöver prata med varandra. I även de enklaste fallen har du i allmänhet åtminstone en tillståndslös webbtjänst och en tillståndskänslig datalagringstjänst som behöver kommunicera.

Service-to-service-kommunikation är en kritisk integrationspunkt för ett program, eftersom varje tjänst exponerar ett fjärr-API för andra tjänster. Att arbeta med en uppsättning API-gränser som involverar I/O kräver i allmänhet viss omsorg, med en bra mängd testning och validering.

Det finns många överväganden att göra när dessa tjänstegränser är kopplade tillsammans i ett distribuerat system:

* *Transportprotokoll*. Kommer du att använda HTTP för ökad interoperabilitet, eller ett anpassat binärt protokoll för maximalt dataflöde?
* *Felhantering*. Hur hanteras permanenta och tillfälliga fel? Vad händer när en tjänst flyttas till en annan nod?
* *Tidsutgångar och svarstid*. I multitiered program, hur kommer varje tjänst lager hantera svarstid genom stacken och till användaren?

Oavsett om du använder en av de inbyggda tjänstkommunikationskomponenterna som tillhandahålls av Service Fabric eller om du bygger dina egna, är det viktigt att testa interaktionerna mellan dina tjänster för att säkerställa återhämtning i ditt program.

## <a name="prepare-for-services-to-move"></a>Förbered för tjänster att flytta
Tjänstinstanser kan röra sig över tid. Detta gäller särskilt när de konfigureras med belastningsmått för skräddarsydd optimal resursbalansering. Service Fabric flyttar dina tjänstinstanser för att maximera deras tillgänglighet även under uppgraderingar, redundans, utskalning och andra situationer som uppstår under ett distribuerat systems livstid.

När tjänster flyttas i klustret bör dina klienter och andra tjänster vara beredda att hantera två scenarier när de talar med en tjänst:

* Tjänstinstansen eller partitionsrepliken har flyttats sedan du senast pratade med den. Detta är en normal del av en tjänstlivscykel och det bör förväntas ske under programmets livstid.
* Tjänstinstansen eller partitionsrepliken håller på att flyttas. Även om redundans av en tjänst från en nod till en annan sker mycket snabbt i Service Fabric, kan det finnas en fördröjning i tillgänglighet om kommunikationskomponenten i tjänsten är långsam att starta.

Hantering av dessa scenarier på ett smidigt sätt är viktigt för ett smidigt system. Tänk på följande:

* Varje tjänst som kan anslutas till har en *adress* som den lyssnar på (till exempel HTTP eller WebSockets). När en tjänstinstans eller partition flyttas ändras adressslutpunkten. (Den flyttas till en annan nod med en annan IP-adress.) Om du använder de inbyggda kommunikationskomponenterna hanterar de omlösande serviceadresser åt dig.
* Det kan finnas en tillfällig ökning av servicefördröjningen när tjänstinstansen startar lyssnaren igen. Detta beror på hur snabbt tjänsten öppnar lyssnaren när tjänstinstansen har flyttats.
* Alla befintliga anslutningar måste stängas och öppnas igen när tjänsten öppnas på en ny nod. En graciös nod avstängning eller omstart ger tid för befintliga anslutningar som ska stängas av graciöst.

### <a name="test-it-move-service-instances"></a>Testa det: Flytta tjänstinstanser
Genom att använda Service Fabric testability verktyg kan du skapa ett testscenario för att testa dessa situationer på olika sätt:

1. Flytta en tillståndskänslig tjänsts primära replik.
   
    Den primära repliken för en tillståndskänslig tjänstpartition kan flyttas av många orsaker. Använd detta för att rikta den primära repliken av en viss partition för att se hur dina tjänster reagerar på flytten på ett mycket kontrollerat sätt.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Stoppa en nod.
   
    När en nod stoppas flyttar Service Fabric alla tjänstinstanser eller partitioner som fanns på noden till en av de andra tillgängliga noderna i klustret. Använd detta för att testa en situation där en nod går förlorad från klustret och alla tjänstinstanser och repliker på den noden måste flytta.
   
    Du kan stoppa en nod med hjälp av PowerShell **Stop-ServiceFabricNode** cmdlet:
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Underhåll tjänstens tillgänglighet
Som plattform är Service Fabric utformat för att ge hög tillgänglighet till dina tjänster. Men i extrema fall kan underliggande infrastrukturproblem fortfarande orsaka otillgänglighet. Det är viktigt att testa för dessa scenarier också.

Tillståndskänsliga tjänster använder ett kvorumbaserat system för att replikera tillstånd för hög tillgänglighet. Det innebär att kvorum av repliker måste vara tillgängliga för att utföra skrivåtgärder. I sällsynta fall, till exempel ett omfattande maskinvarufel, kanske inte kvorum av repliker är tillgängliga. I dessa fall kommer du inte att kunna utföra skrivåtgärder, men du kommer fortfarande att kunna utföra läsåtgärder.

### <a name="test-it-write-operation-unavailability"></a>Testa det: Skriv drift otillgänglighet
Genom att använda testitetsverktygen i Service Fabric kan du injicera ett fel som framkallar kvorumförlust som ett test. Även om ett sådant scenario är sällsynt, är det viktigt att klienter och tjänster som är beroende av en tillståndskänslig tjänst är beredda att hantera situationer där de inte kan göra skrivbegäranden till den. Det är också viktigt att den statsmedvetna tjänsten själv är medveten om denna möjlighet och kan graciöst kommunicera den till de som ringer.

Du kan inducera kvorumförlust med hjälp av cmdleten PowerShell **Invoke-ServiceFabricPartitionQuorumLoss:**

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

I det här `QuorumLossMode` exemplet `QuorumReplicas` anger vi att vi vill inducera kvorumförlust utan att ta bort alla repliker. På så sätt är läsåtgärder fortfarande möjliga. Om du vill testa ett scenario där en hel `AllReplicas`partition inte är tillgänglig kan du ställa in den här växeln på .

## <a name="next-steps"></a>Nästa steg
[Läs mer om testbarhetsåtgärder](service-fabric-testability-actions.md)

[Läs mer om testabilityscenarier](service-fabric-testability-scenarios.md)

