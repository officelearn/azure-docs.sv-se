---
title: Simulera fel i Azure-mikrotjänster | Microsoft Docs
description: Den här artikeln handlar om testningsåtgärder finns i Microsoft Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: motanv
manager: timlt
editor: heeldin
ms.assetid: ed53ca5c-4d5e-4b48-93c9-e386f32d8b7a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: 70ed1561af6dc06b4d1db89e6449540dd76b67be
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55815890"
---
# <a name="testability-actions"></a>Testningsåtgärder
För att simulera en otillförlitliga infrastruktur, tillhandahåller Azure Service Fabric utvecklaren med sätt att simulera olika verkliga fel och tillståndsövergångar. Dessa är exponerade som testbara åtgärder. Åtgärderna som finns på låg nivå API: erna som orsakar en specifik felinmatning eller tillståndsövergång verifiering. Genom att kombinera de här åtgärderna kan skriva du omfattande test-scenarier för dina tjänster.

Service Fabric innehåller några vanliga scenarier för testning består av de här åtgärderna. Vi rekommenderar starkt att du använder de här inbyggda scenarierna som noga valt att testa vanliga tillståndsövergångar och fel fall. Åtgärder kan dock användas för att skapa anpassade testscenarier när du vill lägga till täckning för scenarier som inte omfattas av de inbyggda scenarierna ännu eller som är anpassade skräddarsydd för ditt program.

C#implementeringar av åtgärder finns i System.Fabric.dll-sammansättningen. System Fabric PowerShell-modulen finns i Microsoft.ServiceFabric.Powershell.dll-sammansättningen. ServiceFabric PowerShell-modulen installeras som en del av runtime-installation, för att tillåta för enkel användning.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Korrekt kontra okontrollerad fault-åtgärder
Testningsåtgärder indelas i två större buckets.

* Okontrollerad fel: Dessa fel simulera fel som omstarter av datorn och bearbeta krascher. I sådana fall fel körningssammanhang för processen plötsligt. Det innebär att ingen rensning av tillståndet kan köras innan programmet startas igen.
* Korrekt fel: Dessa fel simulera korrekt åtgärder som repliken flyttar och släpper som utlöses av Utjämning av nätverksbelastning. I sådana fall kan tjänsten hämtar ett meddelande i slutet och rensa upp tillståndet innan du avslutar.

För bättre kvalitet verifiering, köra arbetsbelastningen i tjänsten och företag när förmå olika korrekt och okontrollerad fel. Okontrollerad fel utöva scenarier där tjänstprocessen tvärt avslutas mitt vissa arbetsflöde. Detta testar du återställningssökvägen när tjänsterepliken har återställts av Service Fabric. Detta hjälper att testa datakonsekvens och huruvida tjänstens tillstånd behålls korrekt efter fel. Den andra uppsättningen fel (korrekt misslyckanden) testa att tjänsten korrekt reagerar på repliker flyttas runt av Service Fabric. Detta testar hantering av avbrott i RunAsync-metod. Tjänsten behöver att söka efter uppsägning token som angetts, korrekt spara sitt tillstånd och avsluta RunAsync-metod.

## <a name="testability-actions-list"></a>Möjligheten att testa åtgärdslistan
| Åtgärd | Beskrivning | Hanterade API: N | PowerShell-cmdlet | Korrekt/okontrollerad fel |
| --- | --- | --- | --- | --- |
| CleanTestState |Tar bort alla testtillstånd från klustret när det gäller en felaktig avstängning av test-drivrutinen. |CleanTestStateAsync |Remove-ServiceFabricTestState |Inte tillämpligt |
| InvokeDataLoss |Startar förlust av data i en tjänstpartition. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Korrekt |
| InvokeQuorumLoss |Placerar en viss tillståndskänslig tjänst-partition i förlorar kvorum. |InvokeQuorumLossAsync |Invoke-ServiceFabricQuorumLoss |Korrekt |
| MovePrimary |Flyttar den angivna primära repliken av en tillståndskänslig tjänst till den angivna klusternoden. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Korrekt |
| MoveSecondary |Flyttar den aktuella sekundär repliken av en tillståndskänslig tjänst till en annan klusternod. |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |Korrekt |
| RemoveReplica |Simulerar ett fel för repliken genom att ta bort en replik från ett kluster. Detta kommer att stänga repliken och kommer att överföra den till rollen ”ingen”, ta bort dess status från klustret. |RemoveReplicaAsync |Remove-ServiceFabricReplica |Korrekt |
| RestartDeployedCodePackage |Simulerar ett fel i koden paketet genom att starta om ett kodpaket som distribuerats på en nod i ett kluster. Detta avbryter kod paketet processen, vilket startar om alla användare service repliker finns i den här processen. |RestartDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |Okontrollerad |
| RestartNode |Simulerar ett nodfel för Service Fabric-kluster genom att starta om en nod. |RestartNodeAsync |Restart-ServiceFabricNode |Okontrollerad |
| RestartPartition |Simulerar ett datacenter inaktiverad eller kluster inaktiverad scenario genom att starta om vissa eller alla repliker för en partition. |RestartPartitionAsync |Restart-ServiceFabricPartition |Korrekt |
| RestartReplica |Simulerar ett fel för repliken genom att starta om en bestående replik i ett kluster, stänga repliken och sedan öppna det igen. |RestartReplicaAsync |Restart-ServiceFabricReplica |Korrekt |
| StartNode |Startar en nod i ett kluster som har redan stoppats. |StartNodeAsync |Start-ServiceFabricNode |Inte tillämpligt |
| StopNode |Simulerar ett nodfel genom att stoppa en nod i ett kluster. Noden förblir nedåt tills Startnod anropas. |StopNodeAsync |Stop-ServiceFabricNode |Okontrollerad |
| ValidateApplication |Verifierar tillgänglighet och hälsotillståndet för alla Service Fabric-tjänster i ett program, vanligtvis efter att vissa fel i systemet. |ValidateApplicationAsync |Test-ServiceFabricApplication |Inte tillämpligt |
| ValidateService |Verifierar tillgänglighet och hälsotillståndet för en Service Fabric-tjänst, vanligtvis efter att vissa fel i systemet. |ValidateServiceAsync |Test-ServiceFabricService |Inte tillämpligt |

## <a name="running-a-testability-action-using-powershell"></a>Kör en testningsåtgärder med hjälp av PowerShell
Den här självstudien visar hur du kör en testningsåtgärder med hjälp av PowerShell. Du kommer lära dig hur du kör en testningsåtgärder mot ett lokalt kluster (en-box) eller ett Azure-kluster. Microsoft.Fabric.Powershell.dll--Service Fabric PowerShell-modulen--installeras automatiskt när du installerar Microsoft Service Fabric MSI. Modulen in automatiskt när du öppnar en PowerShell-kommandotolk.

Självstudiekursen segment:

* [Kör en åtgärd mot en en-box-kluster](#run-an-action-against-a-one-box-cluster)
* [Köra en åtgärd mot ett Azure-kluster](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Kör en åtgärd mot en en-box-kluster
För att köra en testningsåtgärder mot ett lokalt kluster, ansluta till klustret och öppnar PowerShell-Kommandotolken i administratörsläge. Låt oss titta på den **omstart ServiceFabricNode** åtgärd.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Här åtgärden **omstart ServiceFabricNode** körs på en nod med namnet ”Nod1”. Slutförande-läge anger att den inte ska kontrollera om omstart av nod-åtgärden faktiskt har slutförts. Ange slutförande-läge som ”verifiera” leder den för att kontrollera om omstartsåtgärden faktiskt har utförts. Istället för att ange noden efter dess namn direkt, kan du ange det via en partitionsnyckel och vilken typ av repliken, enligt följande:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Starta om ServiceFabricNode** bör användas för att starta om en Service Fabric-nod i ett kluster. Detta förhindrar Fabric.exe-processen, vilket startar om alla system-tjänsten och användaren service replikerna på noden. Med den här API för att testa tjänsten hjälper till att identifiera buggar längs redundans recovery sökvägar. Det hjälper att simulera fel för noder i klustret.

Följande skärmbild visar de **omstart ServiceFabricNode** testmöjlighet kommandot fungerar i praktiken.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Utdata från först **Get-ServiceFabricNode** (en cmdlet från Service Fabric PowerShell-modulen) visar att det lokala klustret har fem noder: Node.1 till Node.5. Efter testningsåtgärder (cmdlet) **omstart ServiceFabricNode** körs på noden med namnet Node.4, ser vi att nodens drifttid har återställts.

### <a name="run-an-action-against-an-azure-cluster"></a>Köra en åtgärd mot ett Azure-kluster
Kör en testningsåtgärder (med hjälp av PowerShell) mot ett Azure-kluster liknar körning av åtgärden mot ett lokalt kluster. Den enda skillnaden är att du behöver ansluta till Azure-kluster först innan du kan köra åtgärden, i stället för att ansluta till det lokala klustret.

## <a name="running-a-testability-action-using-c35"></a>Kör en testningsåtgärder med hjälp av C&#35;
Att köra en testningsåtgärder med C#, måste du ansluta till klustret med hjälp av FabricClient. Hämta sedan de parametrar som behövs för att köra åtgärden. Olika parametrar kan användas för att köra samma åtgärd.
Titta på åtgärden RestartServiceFabricNode, är ett sätt att köra den med hjälp av noden informationen (nodnamnet och noden instans-ID) i klustret.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Parametern förklaring:

* **CompleteMode** anger att läget som inte ska kontrollera om omstartsåtgärden faktiskt har skapats. Ange slutförande-läge som ”verifiera” leder den för att kontrollera om omstartsåtgärden faktiskt har utförts.  
* **OperationTimeout** anger hur lång tid för åtgärden ska slutföras innan en TimeoutException undantagsfel.
* **CancellationToken** gör det möjligt för ett väntande anrop som ska avbrytas.

Du kan ange det via en partitionsnyckel och vilken typ av repliken istället för att ange noden direkt efter dess namn.

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
### <a name="partitionselector"></a>PartitionSelector
PartitionSelector är en hjälptjänst som exponeras i möjligheten att testa och används för att välja en specifik partition som utför någon av testbara åtgärder. Det kan användas för att välja en specifik partition om partitions-ID är känt i förväg. Eller, du kan ange Partitionsnyckeln och åtgärden löser partitions-ID internt. Du har också välja en slumpmässig partition.

Skapa objektet PartitionSelector och markerar du partitionen med någon av metoderna Select * om du vill använda den här hjälpfilen. Skicka sedan i objektet PartitionSelector-API: et som kräver. Om inget alternativ har valts standard till en slumpmässig partition.

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
ReplicaSelector är en hjälptjänst som exponeras i möjligheten att testa och används för att välja en replik som utför någon av testbara åtgärder. Det kan användas för att välja en specifik replik om replik-ID är känt i förväg. Dessutom har möjlighet att välja en primär replik eller en slumpmässig sekundär. ReplicaSelector härleds från PartitionSelector, så du måste välja både repliken och den partition som du vill utföra åtgärden för testning.

Om du vill använda den här helper, skapa ett ReplicaSelector-objekt och ange på sätt som du vill välja repliken och partitionen. Du kan sedan överföra den till API-gränssnitt som kräver. Om inget alternativ har valts standard till en slumpmässig replik- och slumpmässiga partition.

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
* [Testningsscenarier](service-fabric-testability-scenarios.md)
* Så här testar du din tjänst
  * [Simulera fel under tjänstarbetsbelastningar](service-fabric-testability-workload-tests.md)
  * [Kommunikationsfel i tjänst-till-tjänst](service-fabric-testability-scenarios-service-communication.md)

