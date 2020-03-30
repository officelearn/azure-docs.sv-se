---
title: Inducera kaos i servicetygskluster
description: Använda API:er för felinjektion och klusteranalystjänst för att hantera Kaos i klustret.
author: motanv
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: motanv
ms.openlocfilehash: 37b451abd0a519dff17aba9b2d6c42b4762f30cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463167"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Inducera kontrollerade kaos i servicetygskluster
Storskaliga distribuerade system som molninfrastrukturer är till sin natur otillförlitliga. Azure Service Fabric gör det möjligt för utvecklare att skriva tillförlitliga distribuerade tjänster ovanpå en opålitlig infrastruktur. För att skriva robusta distribuerade tjänster ovanpå en opålitlig infrastruktur måste utvecklare kunna testa stabiliteten i sina tjänster medan den underliggande opålitliga infrastrukturen genomgår komplicerade tillståndsövergångar på grund av fel.

[Felinsprutnings- och klusteranalystjänsten](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview) (kallas även felanalystjänsten) ger utvecklare möjlighet att inducera fel för att testa sina tjänster. Dessa riktade simulerade fel, som [att starta om en partition,](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps)kan hjälpa till att utöva de vanligaste tillståndsövergångarna. Men riktade simulerade fel är partiska per definition och därmed kan missa buggar som dyker upp endast i svåra att förutsäga, långa och komplicerade sekvens av tillståndsövergångar. För en opartisk testning kan du använda Chaos.

Kaos simulerar periodiska, interfolierade fel (både graciösa och ogripbara) i hela klustret under längre tidsperioder. Ett graciöst fel består av en uppsättning Api-anrop för Service Fabric, till exempel är omstartsreplikfel ett graciöst fel eftersom detta är ett nära följt av en öppen på en replik. Ta bort replik, flytta primär replik och flytta sekundär replik är de andra graciösa fel som utövas av Chaos. Ungraceful fel är processutgångar, som omstart nod och starta om kodpaket. 

När du har konfigurerat Chaos med hastigheten och typen av fel kan du starta Chaos via C#, Powershell eller REST API för att börja generera fel i klustret och i dina tjänster. Du kan konfigurera Chaos så att det körs under en angiven tidsperiod (till exempel i en timme), varefter Chaos stannar automatiskt, eller så kan du anropa StopChaos API (C#, Powershell eller REST) för att stoppa det när som helst.

> [!NOTE]
> I sin nuvarande form inducerar Chaos endast säkra fel, vilket innebär att i avsaknad av externa fel uppstår kvorumförlust eller dataförlust aldrig.
>

Medan Chaos körs, producerar det olika händelser som fångar tillståndet i körningen för tillfället. En ExecutingFaultsEvent innehåller till exempel alla fel som Chaos har beslutat att köra i den iterationen. En validationFailedEvent innehåller information om ett valideringsfel (hälso- eller stabilitetsproblem) som hittades under valideringen av klustret. Du kan anropa GetChaosReport API (C#, Powershell eller REST) för att få rapporten om Chaos-körningar. Dessa händelser sparas i en [tillförlitlig ordlista](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections), som har en trunkeringsprincip som dikteras av två konfigurationer: **MaxStoredChaosEventCount** (standardvärdet är 25000) och **StoredActionCleanupIntervalInSeconds** (standardvärdet är 3600). Alla *StoredActionCleanupIntervalInSeconds* Chaos-kontroller och alla utom de senaste *MaxStoredChaosEventCount-händelserna* rensas från den tillförlitliga ordlistan.

## <a name="faults-induced-in-chaos"></a>Fel framkallas i Kaos
Kaos genererar fel i hela Service Fabric-klustret och komprimerar fel som ses i månader eller år till några timmar. Kombinationen av interfolierade fel med den höga felfrekvensen hittar hörnfall som annars kan missas. Denna övning av Chaos leder till en betydande förbättring av tjänstens kodkvalitet.

Kaos framkallar fel från följande kategorier:

* Starta om en nod
* Starta om ett distribuerat kodpaket
* Ta bort en replik
* Starta om en replik
* Flytta en primär replik (konfigurerbar)
* Flytta en sekundär replik (konfigurerbar)

Kaos körs i flera iterationer. Varje iteration består av fel och klustervalidering för den angivna perioden. Du kan konfigurera den tid som används för att klustret ska stabiliseras och för att valideringen ska lyckas. Om ett fel hittas i klustervalidering genererar och bevarar Chaos en ValidationFailedEvent med UTC-tidsstämpeln och felinformationen. Tänk dig till exempel en instans av Kaos som är inställd på att köras i en timme med högst tre samtidiga fel. Kaos framkallar tre fel och validerar sedan klusterhälsan. Den itererar genom föregående steg tills det uttryckligen stoppas genom StopChaosAsync API eller en timme passerar. Om klustret blir felfritt i en iteration (det vill än att det inte stabiliseras eller det inte blir felfritt inom den passerade MaxClusterStabilizationTimeout), genererar Chaos en ValidationFailedEvent. Händelsen visar att något har gått fel och kan behöva utredas ytterligare.

För att få vilka fel Chaos inducerad, kan du använda GetChaosReport API (powershell, C # eller REST). API:et får nästa segment i Chaos-rapporten baserat på den skickade fortsättningstoken eller det skickade tidsintervallet. Du kan antingen ange ContinuationToken för att hämta nästa segment i Chaos-rapporten eller så kan du ange tidsintervallet via StartTimeUtc och EndTimeUtc, men du kan inte ange både ContinuationToken och tidsintervallet i samma anrop. När det finns fler än 100 Chaos-händelser returneras Chaos-rapporten i segment där ett segment innehåller högst 100 Chaos-händelser.

## <a name="important-configuration-options"></a>Viktiga konfigurationsalternativ
* **TimeToRun**: Total tid som Chaos körs innan det avslutas med framgång. Du kan stoppa Chaos innan den har körts för TimeToRun-perioden via StopChaos API.

* **MaxClusterStabilizationTimeout**: Den maximala tiden att vänta på att klustret ska bli felfritt innan du producerar en ValidationFailedEvent. Denna väntan är att minska belastningen på klustret medan den återställs. De kontroller som utförs är:
  * Om klusterhälsan är OK
  * Om tjänstens hälsotillstånd är OK
  * Om målreplikuppsättningsstorleken uppnås för tjänstpartitionen
  * Att det inte finns några InBuild-repliker
* **MaxConcurrentFaults**: Det maximala antalet samtidiga fel som induceras i varje iteration. Ju högre siffra, desto mer aggressiv Chaos är och redundans och tillståndsövergångkombinationer som klustret går igenom är också mer komplexa. 

> [!NOTE]
> Oavsett hur högt värde *MaxConcurrentFaults* har, garanterar Chaos - i avsaknad av externa fel - det finns ingen kvorumförlust eller dataförlust.
>

* **EnableMoveReplicaFaults**: Aktiverar eller inaktiverar de fel som gör att de primära eller sekundära replikerna flyttas. Dessa fel är aktiverade som standard.
* **WaitTimeBetweenIterations**: Hur lång tid att vänta mellan iterationer. Det innebär att den tid Kaos pausas efter att ha utfört en omgång fel och avslutat motsvarande validering av hälsotillståndet för klustret. Ju högre värde, desto lägre är den genomsnittliga felinsprutningshastigheten.
* **WaitTimeBetweenFaults**: Hur lång tid det ska ta mellan två på varandra följande fel i en enda iteration. Ju högre värde, desto lägre samtidighet (eller överlappningen mellan) fel.
* **ClusterHealthPolicy**: Klusterhälsoprincip används för att validera hälsotillståndet för klustret mellan Chaos-iterationer. Om klusterhälsan är felaktig eller om ett oväntat undantag inträffar under felkörning väntar Chaos i 30 minuter före nästa hälsokontroll - för att ge klustret lite tid att återhämta sig.
* **Sammanhang**: En samling (sträng, sträng) typ nyckel-värde par. Kartan kan användas för att registrera information om Chaos-körningen. Det kan inte finnas fler än 100 sådana par och varje sträng (nyckel eller värde) kan vara högst 4095 tecken lång. Den här kartan ställs in av startmotorn för Chaos-körningen för att eventuellt lagra kontexten om den specifika körningen.
* **ChaosTargetFilter**: Det här filtret kan endast användas för att rikta kaosfel till vissa nodtyper eller endast till vissa programinstanser. Om ChaosTargetFilter inte används, fel chaos alla kluster entiteter. Om ChaosTargetFilter används, chaos fel endast de entiteter som uppfyller ChaosTargetFilter specifikationen. NodeTypeInclusionList och ApplicationInclusionList tillåter endast unionssemantik. Med andra ord är det inte möjligt att ange en skärningspunkt mellan NodeTypeInclusionList och ApplicationInclusionList. Det går till exempel inte att ange "fel det här programmet endast när det är på den nodtypen." När en entitet har inkluderats i nodeTypeInclusionList eller ApplicationInclusionList kan den entiteten inte uteslutas med ChaosTargetFilter. Även om applicationX inte visas i ApplicationInclusionList kan det i vissa Chaos iteration applicationX vara fel eftersom det råkar vara på en nod av nodeTypeY som ingår i NodeTypeInclusionList. Om både NodeTypeInclusionList och ApplicationInclusionList är null eller tomma, genereras ett ArgumentException.
    * **NodeTypeInclusionList**: En lista över nodtyper som ska inkluderas i Kaosfel. Alla typer av fel (omstartsnod, omstart av kodpaket, ta bort replik, starta om replik, flytta primärt och flytta sekundärt) är aktiverade för noderna för dessa nodtyper. Om en nodtyp (säg nodeTypeX) inte visas i NodeTypeInclusionList aktiveras nodnivåfel (som NodeRestart) aldrig för noderna nodTypeX, men kodpaket och replikfel kan fortfarande aktiveras för NodeTypeX om ett program i ApplicationInclusionList råkar finnas på en nod node Av NodeTypeX. Högst 100 nodtypnamn kan inkluderas i den här listan, för att öka det här antalet krävs en konfigurationsuppgradering för MaxNumberOfNodeTypesInChaosTargetFilter-konfiguration.
    * **ApplicationInclusionList**: En lista över program-URI:er som ska inkluderas i Kaosfel. Alla repliker som tillhör tjänster av dessa program är mottagliga för replikfel (starta om replik, ta bort replik, flytta primärt och flytta sekundärt) av Chaos. Kaos kan starta om ett kodpaket endast om kodpaketet endast är värd för repliker av dessa program. Om ett program inte visas i den här listan kan det fortfarande vara fel i vissa Chaos iteration om programmet hamnar på en nod av en nodtyp som ingår i NodeTypeInclusionList. Men om applicationX är knuten till nodeTypeY genom placeringsbegränsningar och applicationX saknas från ApplicationInclusionList och nodeTypeY saknas från NodeTypeInclusionList, kommer applicationX aldrig att fel. Högst 1000 programnamn kan inkluderas i den här listan, för att öka detta antal krävs en konfigurationsuppgradering för MaxNumberOfApplicationsInChaosTargetFilter-konfiguration.

## <a name="how-to-run-chaos"></a>Hur man kör Chaos

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
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster
            // entities. 
            // During validation if a cluster entity is not stable and healthy within
            // MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex
            // series of states to uncover bugs.
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be
            // used to record information about the Chaos run. There cannot be more than 100 such pairs and
            // each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the
            // lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration.
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of
            // state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica,
            // and move secondary replica will happen for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList,
            // restart code package, restart replica, move primary replica, move secondary replica faults will
            // happen for 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing
            // on a node which is not of type included in nodeypeInclusionList.
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
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds,
# Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration.
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of
# states to uncover bugs.
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the
# fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration.
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state
# transitions that the cluster goes through.
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record
# information about the Chaos run.
# There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move
# secondary replica will happen for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for
# 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is
# not of type included in nodeypeInclusionList.
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
