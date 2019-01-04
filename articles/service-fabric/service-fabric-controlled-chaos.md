---
title: Framkalla Chaos i Service Fabric-kluster | Microsoft Docs
description: 'Med felinmatning och klustret Analysis Service API: er för att hantera Chaos i klustret.'
services: service-fabric
documentationcenter: .net
author: motanv
manager: anmola
editor: motanv
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/05/2018
ms.author: motanv
ms.openlocfilehash: a1b334b34e8e234d9ce5cc5ad5cd77bf5ba7118c
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555531"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Framkalla kontrollerat kaos i Service Fabric-kluster
Storskaliga distribuerade system som molninfrastrukturer sin natur instabilt. Azure Service Fabric kan utvecklare skriva tillförlitliga distribuerade tjänster på ett instabilt infrastruktur. Om du vill skriva robust distribuerade tjänster på ett instabilt infrastruktur, utvecklare som behöver kunna testa stabiliteten för sina tjänster medan den underliggande infrastrukturen för otillförlitliga genomgår komplicerade tillståndsövergångar på grund av fel.

Den [felinmatning och klustret Analysis Service](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview) (kallas även Fault Analysis Service) ger utvecklare möjlighet att orsaka fel för att testa sina tjänster. Dessa mål simulerade fel, som [startar om en partition](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), kan hjälpa dig att arbeta med de vanligaste tillståndsövergångar. Men buggar riktade simulerade fel prioriterar per definition och därför kan missa som visar upp endast i svårt att förutse, långa och komplicerade serie ändrar status. Du kan använda Chaos för en oprioriterad testning.

Chaos simulerar periodiska, överlagrad fel (korrekt och okontrollerad) i hela klustret under en längre tid. Ett korrekt fel består av en uppsättning med Service Fabric-API-anrop, till exempel omstart repliken felet är ett korrekt fel eftersom det här är en Stäng följt av en öppen på en replik. Ta bort repliken, flytta primära repliken och flytta sekundär replik är andra korrekt fel som utövar Chaos. Okontrollerad felen är processen avslutas, som att starta om nod och starta om kodpaketet. 

När du har konfigurerat Chaos med hastigheten och vilken typ av fel kan börja du Chaos via C#, Powershell eller REST API till att börja generera fel i klustret och dina tjänster. Du kan konfigurera Chaos ska köras under en angiven tidsperiod (exempelvis under en timme), efter vilken Chaos stoppas automatiskt, eller du kan anropa StopChaos API (C#, Powershell eller REST) att stoppa den när som helst.

> [!NOTE]
> I sin nuvarande form startar Chaos endast säkra fel, vilket innebär att om externa fel en förlorar kvorum eller förlust av data aldrig sker.
>

Medan Chaos körs skapar olika händelser som samlar in tillståndet för kör för tillfället. Till exempel innehåller en ExecutingFaultsEvent alla fel som Chaos har beslutat att köra i den iterationen. En ValidationFailedEvent innehåller information om verifieringsfel (problem med hälsotillstånd eller stabilitet) som hittades under verifieringen av klustret. Du kan anropa GetChaosReport API (C#, Powershell eller REST) för att hämta rapport över Chaos körningar. De här händelserna hämta sparas i en [tillförlitlig ordlista](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections), som har en princip för trunkering enligt två konfigurationer: **MaxStoredChaosEventCount** (standardvärdet är 25000) och **StoredActionCleanupIntervalInSeconds** (standardvärdet är 3600). Varje *StoredActionCleanupIntervalInSeconds* Chaos kontroller och alla men den senaste *MaxStoredChaosEventCount* händelser, rensas från en tillförlitlig ordlista.

## <a name="faults-induced-in-chaos"></a>Fel i Chaos
Chaos genererar fel över hela Service Fabric-klustret och komprimerar fel som visas i månader eller år i några timmar. Kombinationen av överlagrad fel med hög fel hittar specialfall som annars kan missas. Den här övningen Chaos leder till en viktig förbättringar i kod tjänstkvaliteten.

Chaos startar fel från följande kategorier:

* Starta om en nod
* Starta om en distribuerad kodpaketet
* Ta bort en replik
* Starta om en replik
* Flytta en primär replik (kan konfigureras)
* Flytta en sekundär replik (kan konfigureras)

Chaos körs i flera iterationer. Varje iteration består av fel och Klustervalidering för den angivna perioden. Du kan konfigurera den tid som krävs för klustret stabilisera och verifiering för att lyckas. Om ett fel hittas i klusterverifiering Chaos genererar och kvarstår en ValidationFailedEvent med UTC-tidsstämpel och information om felet. Anta exempelvis att en instans av Chaos som är konfigurerat för körning i en timme med högst tre samtidiga fel. Chaos startar tre fel och verifierar sedan hälsotillståndet för klustret. Den går igenom föregående steg förrän den har uttryckligen stoppad via StopChaosAsync API eller en timmes skickar. Om klustret blir ohälsosamt i eventuella iterationer (det vill säga den inte stabilisera eller blir alltså inte felfri inom MaxClusterStabilizationTimeout skickas i), Chaos genererar en ValidationFailedEvent. Den här händelsen anger att något har gått fel och kan behöva ytterligare utredning.

För att få vilka fel som Chaos initierats kan använda du GetChaosReport API (powershell, C# eller REST). API: et hämtar Chaos rapporten utifrån skickas i fortsättningstoken eller skickades i-tidsintervallet nästa segment. Du kan antingen ange ContinuationToken för att hämta nästa segment Chaos rapporten eller du kan ange tidsintervall via StartTimeUtc och EndTimeUtc, men du kan inte ange både ContinuationToken och tidsintervallet i samma anropet. När det finns fler än 100 Chaos händelser, returneras Chaos rapporten i segment där ett segment innehåller fler än 100 Chaos-händelser.

## <a name="important-configuration-options"></a>Viktiga konfigurationsalternativ
* **TimeToRun**: Sammanlagd tid som Chaos körs innan den är klar med framgång. Du kan stoppa Chaos innan den har körts under TimeToRun via StopChaos API.

* **MaxClusterStabilizationTimeout**: Längsta tid att vänta på att klustret fungerar felfritt innan du skapar en ValidationFailedEvent. Den här Vänteperioden är att minska belastningen på klustret medan den håller på att återställas. De kontroller som utförs är:
  * Om hälsan för klustret är OK
  * Om tjänstehälsa är OK
  * Om målet replikuppsättningens storleken uppnås för service-partition
  * Att det finns inga InBuild-repliker
* **MaxConcurrentFaults**: Det maximala antalet samtidiga fel som initierats i varje iteration. Den högre nummer, desto mer aggressivt Chaos är redundansväxlingarna och de tillstånd övergång kombinationer som klustret genomgår är även mer komplexa. 

> [!NOTE]
> Oavsett hur högt värde *MaxConcurrentFaults* har, Chaos garanterar - om externa fel - det finns ingen förlorar kvorum eller förlust av data.
>

* **EnableMoveReplicaFaults**: Aktiverar eller inaktiverar de fel som orsakar att flytta primära eller sekundära replikerna. Dessa fel är aktiverat som standard.
* **WaitTimeBetweenIterations**: Hur lång tid som ska förflyta mellan iterationer. Det vill säga pausar tiden Chaos efter att ha körs ett omgång av fel och har slutfört motsvarande valideringen av hälsotillståndet för klustret. Ju högre värde, Ju lägre är den genomsnittliga fel inmatning hastigheten.
* **WaitTimeBetweenFaults**: Hur lång tid som ska förflyta mellan två på varandra följande fel i en enda iteration. Ju högre värdet är, desto lägre samtidighet på (eller överlapp mellan) faults.
* **ClusterHealthPolicy**: Klustret hälsoprincip används för att kontrollera hälsotillståndet för klustret mellan Chaos iterationer. Om hälsotillståndet för klustret är felaktigt eller om ett oväntat undantag inträffar vid körning av fel, väntar kaos under 30 minuter innan nästa-hälsokontrollen - att ge lite tid att recuperate klustret.
* **Kontext**: En samling (sträng, sträng) Ange nyckel / värde-par. Kartan kan användas för att registrera information om Chaos-körningen. Det får inte finnas fler än 100 par och varje sträng (nyckel eller ett värde) får vara högst 4095 tecken långt. Den här kartan anges med starter Chaos kör för att lagra kontext om den specifika körningen.
* **Chaostargetfilter får**: Det här filtret kan användas för att målet Chaos fel endast för vissa nodtyper eller bara för vissa instanser av programmet. Om chaostargetfilter får inte används, faults Chaos alla entiteter i klustret. Om du använder chaostargetfilter får faults Chaos bara de enheter som uppfyller chaostargetfilter får-specifikationen. Både NodeTypeInclusionList och ApplicationInclusionList kan union semantik. Med andra ord, går det inte att ange en skärningspunkt för både NodeTypeInclusionList och ApplicationInclusionList. Exempel: Det går inte att ange ”felanalyser det här programmet endast när det är på den nodtypen”. När en entitet som ingår i både NodeTypeInclusionList och ApplicationInclusionList kan entiteten inte uteslutas med chaostargetfilter får. Även om applicationX inte visas i ApplicationInclusionList, i vissa Chaos iteration kan applicationX vara fel eftersom det rör sig på en nod i nodeTypeY som ingår i både NodeTypeInclusionList. Om både NodeTypeInclusionList och ApplicationInclusionList är null eller tomt, genereras ett ArgumentException.
    * **Både NodeTypeInclusionList**: En lista över nodtyper ska ingå i Chaos fel. Alla typer av fel (starta om nod, starta om codepackage, ta bort repliken, starta om repliken, flytta primära och sekundära) är aktiverade för noder av typerna noden. Om en nodetype (säga NodeTypeX) visas inte i NodeTypeInclusionList, och sedan noden på fel (till exempel NodeRestart) aldrig ska aktiveras för noderna i NodeTypeX, men paketet och repliken fel i koden kan fortfarande aktiveras för NodeTypeX om ett program i den ApplicationInclusionList råkar finnas på en nod i NodeTypeX. Högst 100 noden typnamnen kan tas med i listan om du vill öka antalet, en uppgradering av konfiguration krävs för MaxNumberOfNodeTypesInChaosTargetFilter konfiguration.
    * **ApplicationInclusionList**: En lista över program URI: er ska ingå i Chaos fel. Alla repliker som hör till tjänster av dessa program är lämpar sig för replik-fel (starta om replikeringen, ta bort repliken, flytta primär och flytta sekundär) av Chaos. Chaos kan behöva starta om ett kodpaket endast om kodpaketet är värd för repliker av dessa program endast. Om ett program inte visas i den här listan, kan det fortfarande vara fel i vissa Chaos iteration om programmet slutar på en nod i en nodtyp som ingår i både NodeTypeInclusionList. Men om applicationX är knuten till nodeTypeY via placeringsbegränsningar och applicationX saknas från ApplicationInclusionList och nodeTypeY saknas från NodeTypeInclusionList, sedan applicationX ska aldrig vara felaktiga. Högst 1000 programnamn kan tas med i listan om du vill öka antalet, en uppgradering av konfiguration krävs för MaxNumberOfApplicationsInChaosTargetFilter konfiguration.

## <a name="how-to-run-chaos"></a>Så här kör du Chaos

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
