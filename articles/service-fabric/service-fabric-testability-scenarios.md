---
title: "Skapa chaos och redundans tester för Azure mikrotjänster | Microsoft Docs"
description: "Med hjälp av Service Fabric chaos test och redundans, testa scenarier för att framkalla fel och verifiera att tjänster."
services: service-fabric
documentationcenter: .net
author: motanv
manager: rsinha
editor: toddabel
ms.assetid: 8eee7e89-404a-4605-8f00-7e4d4fb17553
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: d06026c750e01ad5825338a78d9af331265f434a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="testability-scenarios"></a>Möjlighet att testa scenarier
Stora distribuerade system som molninfrastrukturer är natur instabilt. Azure Service Fabric ger utvecklare möjligheten att skriva tjänster som körs ovanpå instabilt infrastruktur. För att kunna skriva hög kvalitet services utvecklare behöver kan orsaka sådana instabilt infrastruktur för att testa stabiliteten för sina tjänster.

Fel Analysis Service ger utvecklare möjligheten att orsaka fel åtgärder för att testa tjänster med fel. Dock får riktade simulerade fel du endast hittills. Om du vill ta den testa ytterligare, du kan använda testscenarier i Service Fabric: ett chaos test och ett redundanstest. Dessa scenarier simulera kontinuerlig överlagrad fel, både korrekt och städat i hela klustret under en längre tid. När du har konfigurerat ett test med frekvensen och typ av fel kan du starta den via C#-API: er eller PowerShell, för att generera fel i klustret och din tjänst.

> [!WARNING]
> ChaosTestScenario ersätts av en mer flexibel, service-baserade Chaos. Se den nya artikeln [styrs Chaos](service-fabric-controlled-chaos.md) för mer information.
> 
> 

## <a name="chaos-test"></a>Chaos test
Chaos scenariot genererar fel över hela Service Fabric-kluster. Scenariot komprimerar fel som normalt visas i månader eller år till några timmar. Kombinationen av överlagrad fel med hög feltolerans hastighet hittar specialfall som annars saknas. Detta leder till en betydande förbättringar i koden Tjänstkvalitet.

### <a name="faults-simulated-in-the-chaos-test"></a>Fel simulerade i chaos-test
* Starta om en nod
* Starta om distribuerade kodpaketet
* Ta bort en replik
* Starta om en replik
* Flytta en primär replik (valfritt)
* Flytta en sekundär replik (valfritt)

Chaos testet körs flera iterationer av fel och klustret verifieringar för den angivna tidsperioden. Den tid som krävs för klustret att hålla och verifiering lyckas kan också konfigureras. Scenariot misslyckas när du klickar på ett enda fel i klusterverifieringen.

Tänk dig ett test inställd på att köras under en timme med högst tre samtidiga fel. Testet framkalla tre fel och sedan kontrollera hälsotillstånd för klustret. Testet ska gå igenom föregående steg tills klustret blir ohälsosamt eller skickar en timme. Om klustret blir ohälsosamt i varje iteration, inte hålla d.v.s. inom en konfigurerad tid, testet misslyckas med ett undantag. Det här undantaget anger att något är fel och måste ytterligare undersökning.

I sin nuvarande form startar fel generation motorn i testet chaos endast säker fel. Det innebär att externa fel saknas, ett kvorum eller dataförlust inträffar aldrig.

### <a name="important-configuration-options"></a>Viktiga konfigurationsalternativ
* **TimeToRun**: Total tid som testet ska köras innan du avslutar med framgång. Testet kan slutföras tidigare i stället för en misslyckad validering.
* **MaxClusterStabilizationTimeout**: maximal mängd väntetiden för att klustret ska bli felfri innan testet. De kontroller som utförs är om klustret hälsa är OK, tjänstens hälsa är OK, mål replikuppsättning uppnås för service-partition och inga InBuild-repliker finns.
* **MaxConcurrentFaults**: maximalt antal samtidiga fel framkallas i varje iteration. Ju fler, mer aggressivt testet därför ledde till övergången kombinationer och mer komplexa växling vid fel. Testet garanterar att externa fel saknas det inte kommer att ett kvorum och förlust av data, oavsett hur hög den här konfigurationen är.
* **EnableMoveReplicaFaults**: aktiverar eller inaktiverar de fel som orsakar flytta primära eller sekundära repliker. Dessa fel är inaktiverade som standard.
* **WaitTimeBetweenIterations**: tid som ska förflyta mellan iterationer, dvs. efter en runda av fel och motsvarande validering.

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
Testscenario redundans är en version av chaos test-scenario som riktar sig till en specifik tjänst partition. Den testar effekten av växling vid fel på en specifik tjänst-partition och lämna de andra tjänsterna påverkas inte. När den har konfigurerats med partitionsinformation mål och andra parametrar, körs som ett verktyg för klientsidan som använder antingen C#-API: er eller PowerShell för att generera fel för en partition med tjänsten. Scenariot upprepas i en sekvens av simulerade fel och tjänsten validering när affärslogik som körs på sidan för att tillhandahålla en arbetsbelastning. Ett fel i tjänsten validering indikerar ett problem som kräver ytterligare undersökning.

### <a name="faults-simulated-in-the-failover-test"></a>Fel simulerade i failover-test
* Starta om en distribuerade kodpaketet som partitionen finns
* Ta bort en replik av primära och sekundära eller tillståndslösa instans
* Starta om en primär och sekundär replik (om en beständig tjänst)
* Flytta en primär replik
* Flytta en sekundär replik
* Starta om partitionen

Testet redundans startar ett valt fel och kör sedan verifieringen på en tjänst till dess stabilitet. Testet redundans startar bara ett fel i taget, till skillnad mot möjliga flera fel i chaos test. Testet misslyckas om tjänsten partitionen inte hålla inom den konfigurerade tidsgränsen efter varje fel. Testet startar endast säker fel. Det innebär att externa fel saknas, ett kvorum och förlust av data inte utförs.

### <a name="important-configuration-options"></a>Viktiga konfigurationsalternativ
* **PartitionSelector**: Selector-objekt som anger den partition som ska gälla.
* **TimeToRun**: Total tid som testet ska köras innan du avslutar.
* **MaxServiceStabilizationTimeout**: maximal mängd väntetiden för att klustret ska bli felfri innan testet. De kontroller som utförs är om tjänstens hälsa är OK, mål replikuppsättning uppnås för alla partitioner och inga InBuild-repliker finns.
* **WaitTimeBetweenFaults**: lång tid ska vänta mellan varje fel- och validering cykel.

### <a name="how-to-run-the-failover-test"></a>Hur du kör testet för växling vid fel
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
