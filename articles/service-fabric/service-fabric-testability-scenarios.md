---
title: Skapa kaos- och redundanstester för Azure Service Fabric
description: Använda kaostestscenserna för Service Fabric och redundanstest för att framkalla fel och verifiera tjänsternas tillförlitlighet.
author: motanv
ms.topic: conceptual
ms.date: 10/1/2019
ms.author: motanv
ms.openlocfilehash: 206b02024ad052a12e87cfdf1773815027e8aec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465546"
---
# <a name="testability-scenarios"></a>Scenarier för testbarhet
Stora distribuerade system som molninfrastrukturer är till sin natur otillförlitliga. Azure Service Fabric ger utvecklare möjlighet att skriva tjänster för att köras ovanpå otillförlitliga infrastrukturer. För att kunna skriva tjänster av hög kvalitet måste utvecklare kunna inducera en sådan opålitlig infrastruktur för att testa stabiliteten i sina tjänster.

Felanalystjänsten ger utvecklare möjlighet att inducera felåtgärder för att testa tjänster i närvaro av fel. Men riktade simulerade fel kommer att få dig bara så långt. Om du vill ta testningen vidare kan du använda testscenarierna i Service Fabric: ett kaostest och ett redundanstest. Dessa scenarier simulerar kontinuerliga interfolierade fel, både graciösa och oriktiga, i hela klustret under längre tidsperioder. När ett test har konfigurerats med hastigheten och typen av fel kan det startas via antingen C#API:er eller PowerShell, för att generera fel i klustret och din tjänst.

> [!WARNING]
> ChaosTestScenario ersätts av ett mer motståndskraftigt, tjänstebaserat Kaos. Mer information finns i den nya artikeln [Controlled Chaos.](service-fabric-controlled-chaos.md)
> 
> 

## <a name="chaos-test"></a>Kaos test
Kaosscenariot genererar fel i hela Service Fabric-klustret. Scenariot komprimerar fel som vanligtvis ses i månader eller år till några timmar. Kombinationen av interfolierade fel med den höga felfrekvensen hittar hörnfall som annars missas. Detta leder till en betydande förbättring av tjänstens kodkvalitet.

### <a name="faults-simulated-in-the-chaos-test"></a>Fel simulerade i kaostestet
* Starta om en nod
* Starta om ett distribuerat kodpaket
* Ta bort en replik
* Starta om en replik
* Flytta en primär replik (valfritt)
* Flytta en sekundär replik (valfritt)

Kaostestet kör flera iterationer av fel och klustervalideringar under den angivna tidsperioden. Den tid som klustret har lagt ned på att stabilisera och för att valideringen ska lyckas kan också konfigureras. Scenariot misslyckas när du träffar ett enda fel i klustervalidering.

Tänk dig till exempel en testuppsättning som ska köras i en timme med högst tre samtidiga fel. Testet kommer att framkalla tre fel och sedan validera klustrets hälsa. Testet itererar genom föregående steg tills klustret blir ohälsosamt eller en timme passerar. Om klustret blir felfritt i en iteration, dvs det inte stabiliseras inom en konfigurerad tid, kommer testet att misslyckas med ett undantag. Detta undantag tyder på att något har gått fel och behöver undersökas ytterligare.

I sin nuvarande form inducerar felgenereringsmotorn i kaostestet endast säkra fel. Detta innebär att i avsaknad av externa fel kommer ett kvorum eller dataförlust aldrig att inträffa.

### <a name="important-configuration-options"></a>Viktiga konfigurationsalternativ
* **TimeToRun**: Total tid som testet körs innan det avslutas med framgång. Testet kan avslutas tidigare i stället för ett valideringsfel.
* **MaxClusterStabilizationTimeout**: Maximal tid att vänta på att klustret ska bli felfritt innan testet inte misslyckas. De kontroller som utförs är om klusterhälsa är OK, tjänstens hälsotillstånd är OK, målreplikuppsättningsstorleken uppnås för tjänstpartitionen och inga InBuild-repliker finns.
* **MaxConcurrentFaults**: Maximalt antal samtidiga fel som induceras i varje iteration. Ju högre siffra, desto mer aggressiv testet, vilket resulterar i mer komplexa redundans och kombinationer övergång. Testet garanterar att det i avsaknad av externa fel inte kommer att finnas kvorum eller dataförlust, oavsett hur hög den här konfigurationen är.
* **EnableMoveReplicaFaults**: Aktiverar eller inaktiverar de fel som orsakar flytten av de primära eller sekundära replikerna. Dessa fel är inaktiverade som standard.
* **WaitTimeBetweenIterations**: Tid att vänta mellan iterationer, dvs efter en omgång fel och motsvarande validering.

### <a name="how-to-run-the-chaos-test"></a>Så kör du kaostestet
C#-exempel

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

Service Fabric Powershell-modulen innehåller två sätt att starta ett kaosscenario. `Invoke-ServiceFabricChaosTestScenario`är klientbaserad, och om klientdatorn stängs av halvvägs genom testet kommer inga ytterligare fel att införas. Alternativt finns det en uppsättning kommandon som är avsedda att hålla testet igång i händelse av maskinavstängning. `Start-ServiceFabricChaos`använder en tillståndskänslig och tillförlitlig systemtjänst som kallas FaultAnalysisService, vilket säkerställer att fel kommer att förbli introducerade tills TimeToRun är. `Stop-ServiceFabricChaos`kan användas för att manuellt stoppa `Get-ServiceFabricChaosReport` scenariot och hämta en rapport. Mer information finns i [Azure Service Fabric Powershell-referensen](https://docs.microsoft.com/powershell/module/servicefabric/?view=azureservicefabricps) och [inducera kontrollerat kaos i Service Fabric-kluster](service-fabric-controlled-chaos.md).

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## <a name="failover-test"></a>Redundanstest
Redundanstestscenariot är en version av kaostestscenariot som inriktar sig på en viss tjänstpartition. Den testar effekten av redundans på en viss tjänstpartition samtidigt som andra tjänster inte påverkas. När den har konfigurerats med målpartitionsinformationen och andra parametrar körs den som ett verktyg på klientsidan som använder antingen C#API:er eller PowerShell för att generera fel för en tjänstpartition. Scenariot itererar genom en sekvens av simulerade fel och tjänstvalidering medan din affärslogik körs på sidan för att tillhandahålla en arbetsbelastning. Ett fel i tjänstvalidering indikerar ett problem som behöver undersökas ytterligare.

### <a name="faults-simulated-in-the-failover-test"></a>Fel simulerade i redundanstestet
* Starta om ett distribuerat kodpaket där partitionen är värd
* Ta bort en primär/sekundär replik eller tillståndslös instans
* Starta om en primär sekundär replik (om en beständig tjänst)
* Flytta en primär replik
* Flytta en sekundär replik
* Starta om partitionen

Redundanstestet inducerar ett valt fel och kör sedan validering på tjänsten för att säkerställa dess stabilitet. Redundanstestet framkallar bara ett fel i taget, i motsats till eventuella flera fel i kaostestet. Om tjänstpartitionen inte stabiliseras inom den konfigurerade tidsgränsen efter varje fel misslyckas testet. Testet framkallar endast säkra fel. Detta innebär att i avsaknad av externa fel kommer ett kvorum eller dataförlust inte att inträffa.

### <a name="important-configuration-options"></a>Viktiga konfigurationsalternativ
* **PartitionSelector**: Selector-objekt som anger den partition som måste riktas.
* **TimeToRun**: Total tid som testet körs innan det avslutas.
* **MaxServiceStabilizationTimeout**: Maximal tid att vänta på att klustret ska bli felfritt innan testet misslyckas. De kontroller som utförs är om tjänstens hälsotillstånd är OK, målreplikuppsättningsstorleken uppnås för alla partitioner och inga InBuild-repliker finns.
* **WaitTimeBetweenFaults**: Tid att vänta mellan varje fel och valideringscykel.

### <a name="how-to-run-the-failover-test"></a>Så här kör du redundanstestet
**C#**

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario failoverScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await failoverScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


**Powershell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```
