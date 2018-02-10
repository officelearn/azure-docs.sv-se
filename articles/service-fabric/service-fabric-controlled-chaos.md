---
title: Framkalla Chaos i Service Fabric-kluster | Microsoft Docs
description: "Använda fel Injection och klustret Analysis Service API: er för att hantera Chaos i klustret."
services: service-fabric
documentationcenter: .net
author: motanv
manager: anmola
editor: motanv
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/05/2018
ms.author: motanv
ms.openlocfilehash: 81206257cb2c7157bbb1ffcf3a79ced7c896ef80
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Framkalla kontrollerade Chaos i Service Fabric-kluster
Stora distribuerade system som molninfrastrukturer är natur instabilt. Azure Service Fabric kan utvecklare skriva tillförlitliga distribuerade tjänster på ett instabilt infrastruktur. Om du vill skriva robust distribuerade tjänster på en infrastruktur med instabilt behöver utvecklare för att kunna testa stabiliteten i sina tjänster medan den underliggande instabilt infrastrukturen gå igenom komplicerade tillståndsövergångar på grund av fel.

Den [fel Injection och analys klustertjänsten](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview) (även kallat fel Analysis Service) gör att utvecklare kan orsaka fel för att testa sina tjänster. Dessa mål simulerade fel, som [startar om en partition](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), kan hjälpa dig att utöva de vanligaste tillståndsövergångar. Men buggar riktade simulerade fel prioriterar per definition och därmed missa som visar upp endast i tillståndsövergångar svårt att förutse, långa och komplicerade ordning. Du kan använda Chaos för en oprioriterad testning.

Chaos simulerar periodiska, överlagrad fel (korrekt och städat) i hela klustret under en längre tid. Ett fel om korrekt består av en uppsättning Service Fabric-API-anrop, till exempel omstart replik fel är en korrekt fel eftersom det är en Stäng följt av en öppen på en replik. Ta bort replik, flytta primära repliken och flytta sekundär replik är andra korrekt fel för Chaos. Städat fel är processen avslutas, som att starta om noden och restrat koden pacakge. 

När du har konfigurerat Chaos med frekvensen och typ av fel, kan du starta Chaos via C#, Powershell eller REST API för att börja generera fel i klustret och dina tjänster. Du kan konfigurera Chaos ska köras under en angiven tidsperiod (till exempel under en timme), efter vilken Chaos stoppar automatiskt eller så kan du anropa StopChaos API (C#, Powershell eller REST) slutar när som helst.

> [!NOTE]
> I sin nuvarande form startar Chaos endast säker fel, vilket innebär att om externa fel en förlorar kvorum eller förlust av data sker aldrig.
>

När Chaos körs, ger olika händelser som Spara tillståndet för körs för tillfället. En ExecutingFaultsEvent innehåller till exempel alla fel som Chaos har valt att köra i den iterationen. En ValidationFailedEvent innehåller information om valideringsfelet (hälsa eller stabilitet problem) som hittades under valideringen av klustret. Du kan anropa GetChaosReport API (C#, Powershell eller REST) för att få rapporten Chaos körs. Dessa händelser hämta kvar i en [tillförlitliga ordlista](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-reliable-services-reliable-collections), som har en trunkering princip enligt två konfigurationer: **MaxStoredChaosEventCount** (standardvärdet är 25000) och **StoredActionCleanupIntervalInSeconds** (standardvärdet är 3600). Varje *StoredActionCleanupIntervalInSeconds* Chaos kontroller och alla men den senaste *MaxStoredChaosEventCount* händelser, rensas från tillförlitliga ordlistan.

## <a name="faults-induced-in-chaos"></a>Fel i Chaos
Chaos genererar fel över hela Service Fabric-kluster och komprimerar fel som visas i månader eller år till några timmar. Kombinationen av överlagrad fel med hög feltolerans hastighet hittar specialfall som annars kan missas. Den här övningen kaotisk leder till en betydande förbättringar i koden Tjänstkvalitet.

Chaos startar fel i följande kategorier:

* Starta om en nod
* Starta om distribuerade kodpaketet
* Ta bort en replik
* Starta om en replik
* Flytta en primär replik (konfigureras)
* Flytta en sekundär replik (konfigureras)

Chaos körs i flera iterationer. Varje iteration består av fel och klusterverifieringen för den angivna perioden. Du kan konfigurera den tid som krävs att hålla klustret och för verifiering ska lyckas. Om det finns ett fel i klusterverifieringen Chaos genererar och en ValidationFailedEvent med UTC-tidsstämpel och information om felet kvarstår. Anta till exempel att en instans av Chaos som är konfigurerat för körning i en timma med högst tre samtidiga fel. Chaos startar tre fel och verifierar hälsa för klustret. Det går igenom den tidigare skickar steg tills den är uttryckligen stoppad via StopChaosAsync API eller en timme. Om klustret blir ohälsosamt i varje iteration (det vill säga den inte hålla eller blir inte felfri inom MaxClusterStabilizationTimeout skickades i) Chaos genererar en ValidationFailedEvent. Den här händelsen tyder på att något är fel kan behöva ytterligare undersökning.

Du kan använda GetChaosReport API (powershell, C# eller REST) för att få vilka fel Chaos framkallas. API: et hämtar Chaos rapporten utifrån skickades i fortsättningstoken eller skickades i-tidsintervallet nästa segment. Du kan antingen ange ContinuationToken för att få rapporten Chaos nästa segment eller du kan ange tidsintervall via StartTimeUtc och EndTimeUtc, men du kan inte ange både ContinuationToken och tidsintervallet i samma anropet. När det finns fler än 100 Chaos händelser, returneras Chaos rapporten segment där ett segment innehåller fler än 100 Chaos händelser.

## <a name="important-configuration-options"></a>Viktiga konfigurationsalternativ
* **TimeToRun**: Total tid som Chaos kör innan den är klar med framgång. Du kan stoppa Chaos innan den har körts för perioden TimeToRun via StopChaos-API.

* **MaxClusterStabilizationTimeout**: maximal mängd väntetiden för att klustret ska bli felfri innan du skapar en ValidationFailedEvent. Den här vänta är att minska belastningen på klustret när den återställs. De kontroller som utförs är:
  * Om klustret hälsa är OK
  * Om tjänstens hälsa är OK
  * Om mål replikuppsättningen storleken uppnås för service-partition
  * Att det inte finns några InBuild-repliker
* **MaxConcurrentFaults**: det maximala antalet samtidiga fel som framkallas i varje iteration. Högre nummer, desto mer aggressivt kaos är redundansväxlingarna och tillstånd övergången kombinationer som klustret passerar är också mer komplexa. 

> [!NOTE]
> Oavsett hur högt värde *MaxConcurrentFaults* har Chaos garanterar - om externa fel - det finns ingen förlorar kvorum eller förlust av data.
>

* **EnableMoveReplicaFaults**: aktiverar eller inaktiverar de fel som orsakar att flytta primära eller sekundära replikerna. Dessa fel är aktiverat som standard.
* **WaitTimeBetweenIterations**: tidsperiod som ska förflyta mellan iterationer. Det vill säga pausar hur lång tid Chaos efter att ha utförs en runda av fel och har slutförts med motsvarande verifieringen av hälsotillståndet för klustret. Ju högre värdet Ju lägre är den genomsnittliga fel injection hastigheten.
* **WaitTimeBetweenFaults**: tidsperiod som ska förflyta mellan två på varandra följande fel i en enda iteration. Ju högre värdet är, desto lägre samtidighet på (eller överlapp mellan) hos.
* **ClusterHealthPolicy**: klustret hälsoprincip används för att kontrollera hälsotillståndet för klustret between Chaos iterationer. Om klustret hälsa är felaktigt eller om ett oväntat undantag inträffar under körning av fel Chaos ska vänta i 30 minuter innan nästa-hälsotillståndskontroll - att ge lite tid att recuperate klustret.
* **Kontexten**: en samling (sträng, sträng) anger nyckel-värdepar. Kartan kan användas för att registrera information om Chaos kör. Det får inte finnas fler än 100 par och varje sträng (nyckel eller ett värde) får innehålla högst 4095 tecken. Den här kartan anges av starter kaotisk kör för att lagra kontexten om specifika kör.
* **ChaosTargetFilter**: det här filtret kan användas för att målet Chaos fel endast vissa nodtyper eller bara vissa programinstanser. Om inte ChaosTargetFilter används hos Chaos alla entiteter i klustret. Om ChaosTargetFilter används hos Chaos de enheter som uppfyller ChaosTargetFilter-specifikationen. NodeTypeInclusionList och ApplicationInclusionList kan union semantik. Med andra ord, går det inte att ange en skärningspunkt för NodeTypeInclusionList och ApplicationInclusionList. T.ex, går det inte att ange ”fault det här programmet endast när det är för att nodtypen”. När en entitet ingår i NodeTypeInclusionList eller ApplicationInclusionList kan entiteten inte uteslutas med ChaosTargetFilter. Även om applicationX inte visas i ApplicationInclusionList, i vissa Chaos iteration kan applicationX vara fel eftersom det sker på en nod i nodeTypeY som ingår i NodeTypeInclusionList. Om både NodeTypeInclusionList och ApplicationInclusionList är null eller tomt, genereras ett ArgumentException.
    * **NodeTypeInclusionList**: en lista över nodtyper som ska ingå i Chaos fel. Alla typer av fel (starta om nod, starta om codepackage, ta bort replik, starta om repliken, flytta primära och sekundära) har aktiverats för noder av nodtyperna. Om en nodetype (säga NodeTypeX) visas inte i NodeTypeInclusionList, och sedan noden nivån fel (till exempel NodeRestart) aldrig ska aktiveras för noderna i NodeTypeX, men koden paketet och repliken fel kan fortfarande aktiveras för NodeTypeX om ett program i den ApplicationInclusionList råkar finnas på en nod i NodeTypeX. Högst 100 typen nodnamn kan ingå i den här listan för att öka antalet, en config-uppgradering krävs för MaxNumberOfNodeTypesInChaosTargetFilter konfiguration.
    * **ApplicationInclusionList**: en lista över program URI: er ska ingå i Chaos fel. Alla repliker som hör till av dessa program lämpar sig för att repliken fel (starta om repliken, ta bort replik, flytta primära och flytta sekundära) genom Chaos. Chaos kan starta om en kodpaketet endast om kodpaketet värd replikerna för dessa program bara. Om ett program inte visas i listan, kan det fortfarande vara fel i vissa Chaos iteration om programmet som hamnar på en nod av typen som är incuded i NodeTypeInclusionList. Men om applicationX är knutna till nodeTypeY via placeringen och applicationX saknas från ApplicationInclusionList och nodeTypeY saknas från NodeTypeInclusionList, sedan applicationX aldrig blir felaktig. Högst 1000 programnamn kan ingå i den här listan för att öka antalet, en config-uppgradering krävs för MaxNumberOfApplicationsInChaosTargetFilter konfiguration.

## <a name="how-to-run-chaos"></a>Hur du kör Chaos

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }
        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities. 
            // During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
            // the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration. 
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica, and move secondary replica will happen
            // for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList, 
            // restart code package, restart replica, move primary replica, move secondary replica faults will happen for 'fabric:/TestApp2'
            // even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is not of type included in nodeypeInclusionList.
            var applicationInclusionList = new List<string> { "fabric:/TestApp2" };

            // List of cluster entities to target for Chaos faults.
            var chaosTargetFilter = new ChaosTargetFilter
            {
                NodeTypeInclusionList = nodetypeInclusionList,
                ApplicationInclusionList = applicationInclusionList
            };

            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy) {ChaosTargetFilter = chaosTargetFilter};

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy. 
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities. 
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration. 
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
# the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move secondary replica will happen
# for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for 'fabric:/TestApp2'
# even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is not of type included in nodeypeInclusionList.
$chaosTargetFilter.ApplicationInclusionList.Add("fabric:/TestApp2")

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy -ChaosTargetFilter $chaosTargetFilter

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}
```
