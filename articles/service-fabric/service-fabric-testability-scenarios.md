---
title: Skapa kaos-och redundanstest för Azure Service Fabric | Microsoft Docs
description: Med Service Fabric-chaos test och redundans, testa scenarier för att ge upphov till fel och verifiera tillförlitligheten för dina tjänster.
services: service-fabric
documentationcenter: .net
author: motanv
manager: rsinha
editor: toddabel
ms.assetid: 8eee7e89-404a-4605-8f00-7e4d4fb17553
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: d12c5097d4ba5e0ccfe0e2b2cbc8ccd758c32d98
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44051297"
---
# <a name="testability-scenarios"></a>Testningsscenarier
Stora distribuerade system som molninfrastrukturer sin natur instabilt. Azure Service Fabric ger utvecklare möjlighet att skriva tjänster köras ovanpå otillförlitliga infrastrukturer. För att kunna skriva högkvalitativa tjänster, utvecklare behöver kunna orsaka sådana otillförlitliga infrastruktur för att testa stabiliteten för sina tjänster.

Fault Analysis Service ger utvecklare möjlighet att orsaka fel-åtgärder för att testa services om det förekommer fel. Dock får riktade simulerade fel du endast hittills. Om du vill dra testning ytterligare, kan du använda test-scenarier i Service Fabric: en chaos-testning och ett redundanstest. De här scenarierna simulera kontinuerlig överlagrad fel, både korrekt och okontrollerad i hela klustret under en längre tid. När ett test har konfigurerats med frekvensen och typ av fel, kan den startas via C# API: er eller PowerShell, för att generera fel i klustret och din tjänst.

> [!WARNING]
> ChaosTestScenario ersätts av en mer robust, service-baserad Chaos. Se den nya artikeln [kontrolleras Chaos](service-fabric-controlled-chaos.md) för mer information.
> 
> 

## <a name="chaos-test"></a>Chaos test
Chaos scenariot genererar fel över hela Service Fabric-klustret. Scenariot komprimerar fel som är allmänt sett i månader eller år till några timmar. Kombinationen av överlagrad fel med hög fel hittar specialfall som annars missat. Detta leder till en viktig förbättringar i kod tjänstkvaliteten.

### <a name="faults-simulated-in-the-chaos-test"></a>Fel simulerade i testet chaos
* Starta om en nod
* Starta om en distribuerad kodpaketet
* Ta bort en replik
* Starta om en replik
* Flytta en primär replik (valfritt)
* Flytta en sekundär replik (valfritt)

Chaos testet körs flera iterationer av fel och klustret verifieringar för den angivna tidsperioden. Den tid som krävs för klustret stabilisera och verifiering för att lyckas kan också konfigureras. Det går inte att scenariot när du når ett enstaka fel i klustervalideringen.

Tänk dig ett test inställd på att köras under en timme med högst tre samtidiga fel. Testet kan medföra tre fel och sedan Validera klusterhälsa. Testet ska gå igenom föregående steg tills klustret blir ohälsosamt eller skickar en timme. Om klustret blir ohälsosamt i eventuella iterationer, dvs. stabilisera inte inom en konfigurerad tidsperiod, testet misslyckas med ett undantag. Det här undantaget indikerar att något har gått fel och måste ytterligare utredning.

I sin nuvarande form startar fel generation motorn i testet chaos endast säkra fel. Det innebär att om externa fel, ett kvorum eller dataförluster aldrig utförs.

### <a name="important-configuration-options"></a>Viktiga konfigurationsalternativ
* **TimeToRun**: Total tid som testet ska köras innan den avslutar med framgång. Testet kan slutföras tidigare i stället för en misslyckad verifiering.
* **MaxClusterStabilizationTimeout**: maximal mängd väntetiden klustret fungerar felfritt innan testet. De kontroller som utförs är om klusterhälsa är OK, tjänstehälsa är OK, mål för replikuppsättning uppnås för service-partition och inga InBuild-repliker finns.
* **MaxConcurrentFaults**: maximalt antal samtidiga fel initierats i varje iteration. Ju fler, mer aggressivt test, därför vilket resulterar i mer komplexa redundansväxlingar och övergången kombinationer. Testet garanterar inte finns externa fel inte kommer att ett kvorum eller dataförlust, oavsett hur hög den här konfigurationen är.
* **EnableMoveReplicaFaults**: aktiverar eller inaktiverar de fel som orsakar flytta primära eller sekundära repliker. Dessa fel är inaktiverade som standard.
* **WaitTimeBetweenIterations**: tid som ska förflyta mellan iterationer, dvs. efter en omgång av fel och motsvarande verifiering.

### <a name="how-to-run-the-chaos-test"></a>Hur du kör testet chaos
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
Testscenario redundans är en version av chaos test-scenario som riktar sig mot en specifik tjänst-partition. Det testar effekten av redundans på en specifik tjänst-partition och lämna de andra tjänsterna påverkas inte. När den är konfigurerad med partitionsinformation mål och andra parametrar, körs den som ett verktyg för klientsidan som använder C# API: er eller PowerShell för att generera fel för en tjänstpartition. Scenariot går igenom en sekvens med simulerade fel och tjänsteverifieringen medan din affärslogik som körs på sida för att tillhandahålla en arbetsbelastning. Ett fel i tjänsteverifieringen indikerar ett problem som kräver ytterligare undersökning.

### <a name="faults-simulated-in-the-failover-test"></a>Fel simulerade i testet redundans
* Starta om en distribuerad kodpaketet där partitionen finns
* Ta bort en primär/sekundär replik eller tillståndslösa instans
* Starta om en primär, sekundär replik (om en bestående tjänst)
* Flytta en primär replik
* Flytta en sekundär replik
* Starta om partitionen

Redundanstest startar ett valt fel och kör sedan verifiering på tjänsten för att säkerställa dess stabilitet. Redundanstest startar endast ett fel i taget, till skillnad från möjligt flera fel i chaos-testet. Testet misslyckas om service-partitionen inte stabilisera inom den angivna tidsgränsen efter varje fel. Testet startar endast säkra fel. Det innebär att externa fel saknas, ett kvorum eller dataförluster inte utförs.

### <a name="important-configuration-options"></a>Viktiga konfigurationsalternativ
* **PartitionSelector**: väljare-objekt som anger den partition som ska vara mål.
* **TimeToRun**: Total tid som testet ska köras innan du avslutar.
* **MaxServiceStabilizationTimeout**: maximal mängd väntetiden klustret fungerar felfritt innan testet. De kontroller som utförs är om tjänstehälsa är OK, mål för replikuppsättning uppnås för alla partitioner och inga InBuild-repliker finns.
* **WaitTimeBetweenFaults**: tid som ska förflyta mellan varje fel- och verifiering cykel.

### <a name="how-to-run-the-failover-test"></a>Hur du kör testet redundans
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


**PowerShell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```
