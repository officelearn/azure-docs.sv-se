---
title: Simulera fel i Azure-mikrotjänster
description: I den här artikeln beskrivs testabilitetsåtgärderna som finns i Microsoft Azure Service Fabric.
author: motanv
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: 4bdb00eec38addc0c9f88eba8b73185ec5721277
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282047"
---
# <a name="testability-actions"></a>Åtgärder för testbarhet
För att simulera en opålitlig infrastruktur ger Azure Service Fabric dig, utvecklaren, sätt att simulera olika verkliga fel och tillståndsövergångar. Dessa exponeras som testability åtgärder. Åtgärderna är lågnivå-API:er som orsakar en specifik felinjektion, tillståndsövergång eller validering. Genom att kombinera dessa åtgärder kan du skriva omfattande testscenarier för dina tjänster.

Service Fabric innehåller några vanliga testscenarier som består av dessa åtgärder. Vi rekommenderar starkt att du använder dessa inbyggda scenarier, som är noggrant utvalda för att testa vanliga tillståndsövergångar och felfall. Åtgärder kan dock användas för att skapa anpassade testscenarier när du vill lägga till täckning för scenarier som inte omfattas av de inbyggda scenarierna ännu eller som är skräddarsydda för ditt program.

C# implementeringar av åtgärderna finns i System.Fabric.dll-sammansättningen. System Fabric PowerShell-modulen finns i sammansättningen Microsoft.ServiceFabric.Powershell.dll. Som en del av körningsinstallationen installeras ServiceFabric PowerShell-modulen för att underlätta användningen.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Graciösa vs. ungraceful fel handlingar
Testbarhetsåtgärder klassificeras i två större skopor:

* Ungraceful fel: Dessa fel simulerar fel som omstarter av datorn och processkrascher. I sådana fall av fel stoppas körningskontexten för processen plötsligt. Det innebär att ingen rensning av tillståndet kan köras innan programmet startar igen.
* Graciösa fel: Dessa fel simulerar graciösa åtgärder som replikrörelser och droppar som utlöses av belastningsutjämning. I sådana fall får tjänsten ett meddelande om slutet och kan rensa upp i tillståndet innan du avslutar.

För bättre kvalitet validering, kör tjänsten och företagets arbetsbelastning samtidigt förmå olika graciösa och omedvetna fel. Ungraceful fel träningsscenarier där serviceprocessen plötsligt avslutas mitt i vissa arbetsflöden. Detta testar återställningssökvägen när tjänstrepliken återställs av Service Fabric. Detta hjälper till att testa datakonsekvens och om tjänsttillståndet underhålls korrekt efter fel. Den andra uppsättningen fel (de graciösa felen) testar att tjänsten korrekt reagerar på repliker som flyttas runt av Service Fabric. Detta testar hantering av annullering i metoden RunAsync. Tjänsten måste söka efter den avbokningstoken som ställs in, spara tillståndet korrekt och avsluta metoden RunAsync.

## <a name="testability-actions-list"></a>Lista över testabilityåtgärder
| Åtgärd | Beskrivning | Hanterat API | PowerShell-cmdlet | Graciösa/otacksamma fel |
| --- | --- | --- | --- | --- |
| CleanTestState (renteststate) |Tar bort alla testtillstånd från klustret vid felaktig avstängning av testdrivrutinen. |CleanTestStateAsync |Remove-ServiceFabricTestState |Inte tillämpligt |
| InvokeDataLoss |Inducerar dataförlust till en tjänstpartition. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Graciösa |
| InvokeQuorumLoss |Placerar en viss tillståndskänslig tjänstpartition i kvorumförlust. |AnropaQuorumLossAsync |Anropa-ServiceFabricQuorumLoss |Graciösa |
| FlyttaPrimär |Flyttar den angivna primära repliken för en tillståndskänslig tjänst till den angivna klusternoden. |MovePrimaryAsync |Flytta-ServiceFabricPrimaryReplica |Graciösa |
| FlyttaSekys |Flyttar den aktuella sekundära repliken för en tillståndskänslig tjänst till en annan klusternod. |MoveSecondaryAsync |Flytta-ServiceFabricSecondaryReplica |Graciösa |
| Ta bortReplica |Simulerar ett replikfel genom att ta bort en replik från ett kluster. Detta stänger repliken och övergår till rollen "Ingen", vilket tar bort alla dess tillstånd från klustret. |RemoveReplicaAsync |Ta bort serviceFabricReplica |Graciösa |
| Starta omdeployedCodePackage |Simulerar ett kodpaketprocessfel genom att starta om ett kodpaket som distribueras på en nod i ett kluster. Detta avbryter kodpaketprocessen, som startar om alla användartjänstrepliker som finns i den processen. |Starta omdeployedCodePackageAsync |Starta om ServiceFabricDeployedCodePackage |Ungraceful (ungraceful) |
| RestartNode |Simulerar ett klusternod för Service Fabric genom att starta om en nod. |RestartNodeAsync |Starta om-ServiceFabricNode |Ungraceful (ungraceful) |
| Starta ompartition |Simulerar ett datacenter blackout eller kluster blackout scenario genom att starta om några eller alla repliker av en partition. |RestartPartitionAsync |Restart-ServiceFabricPartition |Graciösa |
| RestartReplica |Simulerar ett replikfel genom att starta om en beständig replik i ett kluster, stänga repliken och sedan öppna den igen. |RestartReplicaAsync |Starta om-ServiceFabricReplica |Graciösa |
| StartNod |Startar en nod i ett kluster som redan har stoppats. |StartNodeAsync |Start-ServiceFabricNode |Inte tillämpligt |
| StopNode (StopNode) |Simulerar ett nodfel genom att stoppa en nod i ett kluster. Noden förblir nere tills StartNode anropas. |StopNodeAsync |Stop-ServiceFabricNode |Ungraceful (ungraceful) |
| Valideranapplikation |Validerar tillgängligheten och hälsan för alla Service Fabric-tjänster i ett program, vanligtvis efter att ha förmå ett fel i systemet. |ValideraApplicationAsync |Test-ServiceFabricApplication |Inte tillämpligt |
| Validera tjänst |Validerar tillgängligheten och hälsan för en Service Fabric-tjänst, vanligtvis efter att ha förmå ett fel i systemet. |ValideraServiceAsync |Test-ServiceFabricService |Inte tillämpligt |

## <a name="running-a-testability-action-using-powershell"></a>Köra en testability-åtgärd med PowerShell
Den här självstudien visar hur du kör en testability-åtgärd med hjälp av PowerShell. Du får lära dig hur du kör en testabilitetsåtgärd mot ett lokalt (one-box) kluster eller ett Azure-kluster. Microsoft.Fabric.Powershell.dll - Service Fabric PowerShell-modulen – installeras automatiskt när du installerar Microsoft Service Fabric MSI. Modulen läses in automatiskt när du öppnar en PowerShell-prompt.

Självstudiesegment:

* [Köra en åtgärd mot ett enlrågluster](#run-an-action-against-a-one-box-cluster)
* [Köra en åtgärd mot ett Azure-kluster](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Köra en åtgärd mot ett enlrågluster
Om du vill köra en testabilitetsåtgärd mot ett lokalt kluster ansluter du först till klustret och öppnar PowerShell-prompten i administratörsläge. Låt oss titta på **åtgärden Starta om ServiceFabricNode.**

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Här körs åtgärden **Restart-ServiceFabricNode** på en nod med namnet "Nod1". Slutförandeläget anger att den inte ska kontrollera om åtgärden omstart-nod verkligen lyckades. Om du anger slutförandeläget som "Verifiera" kontrolleras om omstartsåtgärden verkligen lyckades. I stället för att direkt ange noden med dess namn kan du ange den via en partitionsnyckel och typen av replik, enligt följande:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restart-ServiceFabricNode** ska användas för att starta om en Service Fabric-nod i ett kluster. Detta stoppar Fabric.exe-processen, som startar om alla systemtjänst- och användartjänstrepliker som finns på noden. Med hjälp av det här API:et för att testa din tjänst hjälper du till att upptäcka fel längs återställningsvägarna för redundansökning. Det hjälper till att simulera nodfel i klustret.

Följande skärmbild visar kommandot **Restart-ServiceFabricNode** testability i praktiken.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Utdata från den första **Get-ServiceFabricNode** (en cmdlet från Service Fabric PowerShell-modulen) visar att det lokala klustret har fem noder: Nod.1 till Nod.5. När testability-åtgärden (cmdlet) **Restart-ServiceFabricNode** körs på noden, med namnet Nod.4, ser vi att nodens drifttid har återställts.

### <a name="run-an-action-against-an-azure-cluster"></a>Köra en åtgärd mot ett Azure-kluster
Köra en testability åtgärd (med hjälp av PowerShell) mot ett Azure-kluster liknar att köra åtgärden mot ett lokalt kluster. Den enda skillnaden är att innan du kan köra åtgärden, i stället för att ansluta till det lokala klustret, måste du ansluta till Azure-klustret först.

## <a name="running-a-testability-action-using-c35"></a>Köra en testabilityåtgärd med C&#35;
Om du vill köra en testability-åtgärd med C#måste du först ansluta till klustret med Hjälp av FabricClient. Hämta sedan de parametrar som behövs för att köra åtgärden. Olika parametrar kan användas för att köra samma åtgärd.
Om du tittar på åtgärden RestartServiceFabricNode är ett sätt att köra den genom att använda nodinformationen (nodnamn och nodinstans-ID) i klustret.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Parameter förklaring:

* **CompleteMode** anger att läget inte ska kontrollera om omstartsåtgärden verkligen lyckades. Om du anger slutförandeläget som "Verifiera" kontrolleras om omstartsåtgärden verkligen lyckades.  
* **OperationTimeout** anger hur lång tid åtgärden ska avsluta innan ett TimeoutException-undantag genereras.
* **CancellationToken** gör det möjligt att avbryta ett väntande samtal.

I stället för att direkt ange noden med dess namn kan du ange den via en partitionsnyckel och typen av replik.

Mer information finns i PartitionSelector och ReplicaSelector.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector och ReplicaSelector
### <a name="partitionselector"></a>PartitionSelector (PartitionSelector)
PartitionSelector är en hjälpare som exponeras i testbarhet och används för att välja en specifik partition där någon av testability åtgärder. Den kan användas för att välja en specifik partition om partitions-ID är känt i förväg. Du kan också ange partitionsnyckeln och åtgärden kommer att lösa partitions-ID internt. Du har också möjlighet att välja en slumpmässig partition.

Om du vill använda den här hjälpen skapar du PartitionSelector-objektet och markerar partitionen med hjälp av en av Select*-metoderna. Skicka sedan in PartitionSelector-objektet till API:et som kräver det. Om inget alternativ är markerat, är det standard till en slumpmässig partition.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplikaVälktare
ReplicaSelector är en hjälpare exponeras i testbarhet och används för att välja en replik som att utföra någon av testability åtgärder. Den kan användas för att välja en specifik replik om replik-ID:t är känt i förväg. Dessutom har du möjlighet att välja en primär replik eller en slumpmässig sekundär. ReplicaSelector härstammar från PartitionSelector, så du måste välja både repliken och den partition där du vill utföra testability-åtgärden.

Om du vill använda den här hjälpen skapar du ett ReplicaSelector-objekt och anger hur du vill markera repliken och partitionen. Du kan sedan skicka den till API:et som kräver det. Om inget alternativ är markerat, är det standard till en slumpmässig replik och slumpmässig partition.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Nästa steg
* [Scenarier för testbarhet](service-fabric-testability-scenarios.md)
* Så här testar du din tjänst
  * [Simulera fel under tjänstarbetsbelastningar](service-fabric-testability-workload-tests.md)
  * [Kommunikationsfel mellan service och tjänst](service-fabric-testability-scenarios-service-communication.md)

