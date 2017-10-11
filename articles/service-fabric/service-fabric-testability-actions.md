---
title: "Simulera fel i Azure mikrotjänster | Microsoft Docs"
description: "Den här artikeln handlar om datatillgång åtgärder finns i Microsoft Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: motanv
manager: timlt
editor: toddabel
ms.assetid: ed53ca5c-4d5e-4b48-93c9-e386f32d8b7a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv;heeldin
ms.openlocfilehash: c8ddc7732999ae555323bebaef60aa34c8f2ec17
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="testability-actions"></a>Möjlighet att testa åtgärder
För att simulera en instabilt infrastruktur tillhandahåller Azure Service Fabric utvecklaren med olika sätt att simulera olika verkliga fel och tillståndsövergångar. Dessa visas som datatillgång åtgärder. Åtgärderna är för låg nivå API: er som orsakar en viss feltolerans injection, tillståndsövergång eller validering. Du kan skriva omfattande testscenarier för dina tjänster genom att kombinera dessa åtgärder.

Service Fabric innehåller några vanliga testscenarier består av dessa åtgärder. Vi rekommenderar starkt att du använder dessa inbyggda scenarier som väljs noggrant för att testa vanliga tillståndsövergångar och fel fall. Åtgärder kan dock användas för att skapa anpassade testscenarier när du vill lägga till täckning för scenarier som inte omfattas av inbyggda scenarier ännu eller som är anpassade skräddarsydda för ditt program.

C#-implementeringar av åtgärder finns i System.Fabric.dll-sammansättningen. System Fabric PowerShell-modulen finns i Microsoft.ServiceFabric.Powershell.dll-sammansättningen. Modulen ServiceFabric PowerShell installeras som en del av runtime-installation, så att lätt att använda.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Korrekt kontra städat fel åtgärder
Möjlighet att testa åtgärder indelas i två huvudsakliga buckets.

* Städat fel: dessa fel simulera fel som omstarter av datorn och krascher. I sådana fall fel körningskontexten processens plötsligt. Detta innebär att ingen rensning av tillståndet kan köra innan programmet startas igen.
* Korrekt fel: dessa fel simulera korrekt åtgärder som flyttar replik och således som utlösts av belastningsutjämning. I sådana fall kan tjänsten hämtar ett meddelande om stängning och kan rensa tillståndet innan du avslutar.

För bättre kvalitet verifiering, kör du tjänsten och affärskrav arbetsbelastningen när att olika korrekt och städat fel. Städat fel utöva scenarier där tjänstprocessen plötsligt avslutas mitt i vissa arbetsflöde. Detta testar återställningssökvägen när tjänsten replikeringen har återställts av Service Fabric. Detta hjälper testa datakonsekvens och om tjänstens tillstånd hanteras korrekt efter fel. Den andra uppsättningen fel (korrekt misslyckanden) testa att tjänsten korrekt reagerar på repliker flyttas runt av Service Fabric. Detta testar hantering av annullering i metoden RunAsync. Tjänsten måste kontrollera för att ange, spara tillståndet annullering token och avsluta RunAsync-metoden.

## <a name="testability-actions-list"></a>Möjlighet att testa åtgärdslista
| Åtgärd | Beskrivning | Hanterade API | PowerShell-cmdlet | Korrekt/städat fel |
| --- | --- | --- | --- | --- |
| CleanTestState |Tar bort alla testtillstånd från klustret vid en felaktig avstängning av test-drivrutinen. |CleanTestStateAsync |Remove-ServiceFabricTestState |Inte tillämpligt |
| InvokeDataLoss |Startar förlust av data i en partition med tjänsten. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Korrekt |
| InvokeQuorumLoss |Placerar en given tillståndskänslig service partition i förlorar kvorum. |InvokeQuorumLossAsync |Anropa ServiceFabricQuorumLoss |Korrekt |
| Flytta primära |Flyttar den angivna primära repliken av en tillståndskänslig tjänst till den angivna klusternoden. |MovePrimaryAsync |Flytta ServiceFabricPrimaryReplica |Korrekt |
| Flytta sekundär |Flyttar den aktuella sekundär repliken av en tillståndskänslig tjänst till en annan klusternod. |MoveSecondaryAsync |Flytta ServiceFabricSecondaryReplica |Korrekt |
| RemoveReplica |Simulerar ett replik fel genom att ta bort en replik från ett kluster. Detta kommer att stängas repliken och kommer övergång till rollen None, ta bort dess status från klustret. |RemoveReplicaAsync |Ta bort ServiceFabricReplica |Korrekt |
| RestartDeployedCodePackage |Simulerar ett fel i koden paketet genom att starta om en kodpaketet har distribuerats på en nod i ett kluster. Detta avbryter kod paketet processen, vilket startar om alla användare service repliker finns i den här processen. |RestartDeployedCodePackageAsync |Starta om ServiceFabricDeployedCodePackage |Städat |
| RestartNode |Simulerar ett nodfel för Service Fabric-kluster genom att starta om en nod. |RestartNodeAsync |Starta om ServiceFabricNode |Städat |
| RestartPartition |Simulerar ett datacenter blackout eller kluster blackout scenario genom att starta om vissa eller alla repliker för en partition. |RestartPartitionAsync |Restart-ServiceFabricPartition |Korrekt |
| RestartReplica |Simulerar ett fel för repliken genom att starta om en beständiga replik i ett kluster, stänga repliken och sedan öppna den igen. |RestartReplicaAsync |Starta om ServiceFabricReplica |Korrekt |
| Startnod |Startar en nod i ett kluster som har redan stoppats. |StartNodeAsync |Start-ServiceFabricNode |Inte tillämpligt |
| StopNode |Simulerar ett nodfel genom att stoppa en nod i ett kluster. Noden förblir ned förrän Startnod anropas. |StopNodeAsync |Stop-ServiceFabricNode |Städat |
| ValidateApplication |Verifierar tillgänglighet och hälsotillståndet för alla Service Fabric-tjänster i ett program, vanligtvis efter att vissa fel i systemet. |ValidateApplicationAsync |Testa ServiceFabricApplication |Inte tillämpligt |
| ValidateService |Verifierar tillgänglighet och hälsotillståndet för ett Service Fabric-tjänsten, vanligtvis efter att vissa fel i systemet. |ValidateServiceAsync |Testa ServiceFabricService |Inte tillämpligt |

## <a name="running-a-testability-action-using-powershell"></a>Köra en datatillgång-åtgärd med hjälp av PowerShell
Den här kursen visar hur du kör en möjlighet att testa åtgärden med hjälp av PowerShell. Du får lära dig att köra en datatillgång-åtgärd mot en lokal (en-box)-kluster eller ett Azure-kluster. Microsoft.Fabric.Powershell.dll--Service Fabric PowerShell-modulen--installeras automatiskt när du installerar Microsoft Service Fabric MSI. Modulen har lästs in automatiskt när du öppnar en PowerShell-kommandotolk.

Självstudiekurs segment:

* [Köra en åtgärd mot ett kluster med en ruta](#run-an-action-against-a-one-box-cluster)
* [Köra en åtgärd mot ett Azure-kluster](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Köra en åtgärd mot ett kluster med en ruta
Om du vill köra en datatillgång-åtgärd mot en lokala klustret, Anslut till klustret och öppna PowerShell-Kommandotolken i administratörsläge. Låt oss titta på den **omstart ServiceFabricNode** åtgärd.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Här åtgärden **omstart ServiceFabricNode** körs på en nod med namnet ”Nod1”. Slutförande-läge anger att den inte ska kontrollera om omstart av nod åtgärden faktiskt har slutförts. Ange slutförande-läge som ”verifiera” gör att den kan kontrollera om omstart åtgärden faktiskt har slutförts. I stället för att direkt ange noden med namnet, kan du ange den via en partitionsnyckel och vilken typ av repliken, enligt följande:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Starta om ServiceFabricNode** ska användas för att starta om en Service Fabric-nod i ett kluster. Detta förhindrar att Fabric.exe-processen, vilket startar om alla system-tjänsten och användaren service repliker finns på noden. Använd detta API för att testa din tjänst kan upptäcka buggar längs failover återställning sökvägar. Det hjälper att simulera nodfel i klustret.

I följande skärmbild visas den **omstart ServiceFabricNode** datatillgång kommandot i åtgärden.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Utdata från först **Get-ServiceFabricNode** (en cmdlet från modulen Service Fabric PowerShell) visar att det lokala klustret har fem noder: Node.1 till Node.5. När du har möjlighet att testa åtgärden (cmdlet) **omstart ServiceFabricNode** körs på noden med namnet Node.4 vi se att nodens drifttid har återställts.

### <a name="run-an-action-against-an-azure-cluster"></a>Köra en åtgärd mot ett Azure-kluster
Kör en datatillgång-åtgärd (med hjälp av PowerShell) mot ett Azure-kluster liknar Kör åtgärden mot lokala klustret. Den enda skillnaden är att du måste först ansluta till Azure-klustret innan du kan köra åtgärden i stället för att ansluta till det lokala klustret.

## <a name="running-a-testability-action-using-c35"></a>Köra en datatillgång åtgärd med C & #35.
Om du vill köra en möjlighet att testa åtgärden med hjälp av C#, måste du först ansluta till klustret med hjälp av FabricClient. Skaffa de parametrar som behövs för att köra instruktionen. Olika parametrar som kan användas för att köra samma åtgärd.
Titta på åtgärden RestartServiceFabricNode är ett sätt att köra den med hjälp av noden informationen (nodnamnet och nod-instans-ID) i klustret.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Parametern förklaring:

* **CompleteMode** anger att läget som inte ska kontrollera om omstart åtgärden faktiskt har slutförts. Ange slutförande-läge som ”verifiera” gör att den kan kontrollera om omstart åtgärden faktiskt har slutförts.  
* **OperationTimeout** anger hur lång tid för att åtgärden ska slutföras innan en TimeoutException undantag.
* **CancellationToken** aktiverar ett väntande samtal avbrytas.

I stället för att direkt ange noden med namnet, kan du ange det via en partitionsnyckel och vilken typ av replikering.

Mer information finns i [PartitionSelector och ReplicaSelector](#partition_replica_selector).

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
### <a name="partitionselector"></a>PartitionSelector
PartitionSelector är en hjälp som visas i datatillgång och används för att välja en specifik partition som du vill utföra några åtgärder för datatillgång. Det kan användas för att välja en specifik partition om partitions-ID är känt i förväg. Eller, du kan ange Partitionsnyckeln och åtgärden kommer att lösa partitions-ID internt. Du har också välja en slumpmässig partition.

Skapa PartitionSelector-objekt och markerar du partitionen med någon av metoderna väljer * om du vill använda den här hjälpfilen. Ange sedan objektet PartitionSelector-API: et som kräver. Om inget alternativ har valts standard till en slumpmässig partition.

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

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector är en hjälp som visas i datatillgång och används för att markera en replik som du vill utföra några åtgärder för datatillgång. Det kan användas för att välja en specifik replik om replik-ID är känt i förväg. Dessutom har möjlighet att välja en primär replik eller en slumpmässig sekundär. ReplicaSelector härleds från PartitionSelector, så du måste välja både repliken och den partition som du vill utföra åtgärden datatillgång.

Skapa ett ReplicaSelector-objekt om du vill använda den här hjälpfilen och ange hur du vill välja repliken och partitionen. Du kan sedan överföra den till API-gränssnitt som kräver. Om inget alternativ har valts används som standard slumpmässig replik- och slumpmässiga partition.

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
* [Möjlighet att testa scenarier](service-fabric-testability-scenarios.md)
* Hur du testar din tjänst
  * [Simulera fel under tjänstens arbetsbelastningar](service-fabric-testability-workload-tests.md)
  * [Tjänst-till-tjänst kommunikationsfel](service-fabric-testability-scenarios-service-communication.md)

