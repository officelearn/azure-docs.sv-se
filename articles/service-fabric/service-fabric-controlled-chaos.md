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
ms.date: 08/09/2017
ms.author: motanv
ms.openlocfilehash: 3b3b93bc9ec5ecdcfc289e5b62e84de6aa4172ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Framkalla kontrollerade Chaos i Service Fabric-kluster
Stora distribuerade system som molninfrastrukturer är natur instabilt. Azure Service Fabric kan utvecklare skriva tillförlitliga distribuerade tjänster på ett instabilt infrastruktur. Om du vill skriva robust distribuerade tjänster på en infrastruktur med instabilt behöver utvecklare för att kunna testa stabiliteten i sina tjänster medan den underliggande instabilt infrastrukturen gå igenom komplicerade tillståndsövergångar på grund av fel.

Den [fel Injection och analys klustertjänsten](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-testability-overview) (även kallat fel Analysis Service) gör att utvecklare kan orsaka fel för att testa sina tjänster. Dessa mål simulerade fel, som [startar om en partition](https://docs.microsoft.com/en-us/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps), kan hjälpa dig att utöva de vanligaste tillståndsövergångar. Men buggar riktade simulerade fel prioriterar per definition och därmed missa som visar upp endast i tillståndsövergångar svårt att förutse, långa och komplicerade ordning. Du kan använda Chaos för en oprioriterad testning.

Chaos simulerar periodiska, överlagrad fel (korrekt och städat) i hela klustret under en längre tid. När du har konfigurerat Chaos med frekvensen och typ av fel, kan du starta Chaos via C# eller Powershell API ska börja generera fel i klustret och dina tjänster. Du kan konfigurera Chaos ska köras under en angiven tidsperiod (till exempel under en timme), efter vilken Chaos stoppar automatiskt eller så kan du anropa StopChaos API (C# eller Powershell) slutar när som helst.

> [!NOTE]
> I sin nuvarande form startar Chaos endast säker fel, vilket innebär att om externa fel en förlorar kvorum eller förlust av data sker aldrig.
>

När Chaos körs, ger olika händelser som Spara tillståndet för körs för tillfället. En ExecutingFaultsEvent innehåller till exempel alla fel som Chaos har valt att köra i den iterationen. En ValidationFailedEvent innehåller information om valideringsfelet (hälsa eller stabilitet problem) som hittades under valideringen av klustret. Du kan anropa GetChaosReport API (C# eller Powershell) för att få rapporten Chaos körs. Dessa händelser hämta kvar i en [tillförlitliga ordlista](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-reliable-services-reliable-collections), som har en trunkering princip enligt två konfigurationer: **MaxStoredChaosEventCount** (standardvärdet är 25000) och **StoredActionCleanupIntervalInSeconds** (standardvärdet är 3600). Varje *StoredActionCleanupIntervalInSeconds* Chaos kontroller och alla men den senaste *MaxStoredChaosEventCount* händelser, rensas från tillförlitliga ordlistan.

## <a name="faults-induced-in-chaos"></a>Fel i Chaos
Chaos genererar fel över hela Service Fabric-kluster och komprimerar fel som visas i månader eller år till några timmar. Kombinationen av överlagrad fel med hög feltolerans hastighet hittar specialfall som annars kan missas. Den här övningen kaotisk leder till en betydande förbättringar i koden Tjänstkvalitet.

Chaos startar fel i följande kategorier:

* Starta om en nod
* Starta om distribuerade kodpaketet
* Ta bort en replik
* Starta om en replik
* Flytta en primär replik (konfigureras)
* Flytta en sekundär replik (konfigureras)

Chaos körs i flera iterationer. Varje iteration består av fel och klusterverifieringen för den angivna perioden. Du kan konfigurera den tid som krävs att hålla klustret och för verifiering ska lyckas. Om det finns ett fel i klusterverifieringen Chaos genererar och en ValidationFailedEvent med UTC-tidsstämpel och information om felet kvarstår. Anta till exempel att en instans av Chaos som är konfigurerat för körning i en timma med högst tre samtidiga fel. Chaos startar tre fel och verifierar hälsa för klustret. Det går igenom den tidigare skickar steg tills den är uttryckligen stoppad via StopChaosAsync API eller en timme. Om klustret blir ohälsosamt i varje iteration (dvs, den inte hålla inom MaxClusterStabilizationTimeout skickades i), Chaos genererar en ValidationFailedEvent. Den här händelsen tyder på att något är fel kan behöva ytterligare undersökning.

Du kan använda GetChaosReport API (powershell eller C#) för att få vilka fel Chaos framkallas. API: et hämtar Chaos rapporten utifrån skickades i fortsättningstoken eller skickades i-tidsintervallet nästa segment. Du kan antingen ange ContinuationToken för att få rapporten Chaos nästa segment eller du kan ange tidsintervall via StartTimeUtc och EndTimeUtc, men du kan inte ange både ContinuationToken och tidsintervallet i samma anropet. När det finns fler än 100 Chaos händelser, returneras Chaos rapporten segment där ett segment innehåller fler än 100 Chaos händelser.

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

* **EnableMoveReplicaFaults**: aktiverar eller inaktiverar de fel som orsakar att flytta primära eller sekundära replikerna. Dessa fel är inaktiverade som standard.
* **WaitTimeBetweenIterations**: tidsperiod som ska förflyta mellan iterationer. Det vill säga pausar hur lång tid Chaos efter att ha utförs en runda av fel och har slutförts med motsvarande verifieringen av hälsotillståndet för klustret. Ju högre värdet Ju lägre är den genomsnittliga fel injection hastigheten.
* **WaitTimeBetweenFaults**: tidsperiod som ska förflyta mellan två på varandra följande fel i en enda iteration. Ju högre värdet är, desto lägre samtidighet på (eller överlapp mellan) hos.
* **ClusterHealthPolicy**: klustret hälsoprincip används för att kontrollera hälsotillståndet för klustret between Chaos iterationer. Om klustret hälsa är felaktigt eller om ett oväntat undantag inträffar under körning av fel Chaos ska vänta i 30 minuter innan nästa-hälsotillståndskontroll - att ge lite tid att recuperate klustret.
* **Kontexten**: en samling (sträng, sträng) anger nyckel-värdepar. Kartan kan användas för att registrera information om Chaos kör. Det får inte finnas fler än 100 par och varje sträng (nyckel eller ett värde) får innehålla högst 4095 tecken. Den här kartan anges av starter kaotisk kör för att lagra kontexten om specifika kör.

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
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

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

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

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
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

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
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```
