---
title: Skala ett service fabric-kluster in eller ut
description: Skala ett Service Fabric-kluster in eller ut för att matcha efterfrågan genom att ställa in regler för automatisk skalning för varje nodtyp/virtuell datorskalauppsättning. Lägga till eller ta bort noder i ett Service Fabric-kluster
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 26ef13f38d525e4e493ad933bfb906dd36ed0070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258738"
---
# <a name="scale-a-cluster-in-or-out"></a>Skala in eller ut ett kluster

> [!WARNING]
> Läs det här avsnittet innan du skalar

Skalning av beräkningsresurser för att köpa programarbetsbelastningen kräver avsiktlig planering, tar nästan alltid längre tid än en timme att slutföra för en produktionsmiljö och kräver att du förstår din arbetsbelastning och affärskontext. Faktum är att om du aldrig har gjort den här aktiviteten tidigare, rekommenderar vi att du börjar med att läsa och förstå [service fabric-klusterkapacitetsplaneringsöverväganden](service-fabric-cluster-capacity.md)innan resten av det här dokumentet fortsätter. Den här rekommendationen är att undvika oavsiktliga LiveSite-problem, och det rekommenderas också att du testar de åtgärder som du bestämmer dig för att utföra mot en icke-produktionsmiljö. Du kan när som helst [rapportera produktionsproblem eller begära betald support för Azure](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). För tekniker som har tilldelats för att utföra dessa åtgärder som har ett lämpligt sammanhang beskriver den här artikeln skalningsåtgärder, men du måste bestämma och förstå vilka åtgärder som är lämpliga för ditt användningsfall. till exempel vilka resurser som ska skalas (CPU, Lagring, Minne), vilken riktning som ska skalas (lodrätt eller vågrätt) och vilka åtgärder som ska utföras (resursmallsdistribution, portal, PowerShell/CLI).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Skala ett Service Fabric-kluster in eller ut med hjälp av regler för automatisk skalning eller manuellt
Skala uppsättningar för virtuella datorer är en Azure-beräkningsresurs som du kan använda för att distribuera och hantera en samling virtuella datorer som en uppsättning. Varje nodtyp som definieras i ett Service Fabric-kluster ställs in som en separat skaluppsättning för virtuella datorer. Varje nodtyp kan sedan skalas in eller ut oberoende av varandra, ha olika uppsättningar portar öppna och ha olika kapacitetsmått. Läs mer om det i [dokumentet Service Fabric-nodtyper.](service-fabric-cluster-nodetypes.md) Eftersom nodtyperna Service Fabric i klustret består av skaluppsättningar för virtuella datorer vid serverdelen måste du ställa in regler för automatisk skalning för varje nodtyp/virtuell datorskalauppsättning.

> [!NOTE]
> Din prenumeration måste ha tillräckligt med kärnor för att lägga till de nya virtuella datorer som utgör det här klustret. Det finns ingen modellvalidering för närvarande, så du får ett tidsfel för distribution, om någon av kvotgränserna träffas. Även en enda nodtyp kan inte bara överstiga 100 noder per VMSS. Du kan behöva lägga till VMSS för att uppnå den riktade skalan, och automatisk skalning kan inte automagiskt lägga till VMSS.You may need to add VMSS's to achieve the targeted scale, and auto-scaling can not automagically add VMSS's. Att lägga till VMSS på plats i ett live-kluster är en utmanande uppgift, och detta resulterar ofta i att användare etablerar nya kluster med lämpliga nodtyper som etablerats vid skapande. [planera klusterkapacitet](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) i enlighet med detta. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Välj nodtyp/skala för virtuell dator inställd på skala
För närvarande kan du inte ange reglerna för automatisk skalning för skalningsuppsättningar för virtuella datorer med hjälp av portalen för att skapa ett Service Fabric-kluster, så låt oss använda Azure PowerShell (1.0+) för att lista nodtyperna och sedan lägga till regler för automatisk skalning till dem.

Om du vill hämta en lista över skalningsuppsättning för virtuella datorer som utgör klustret kör du följande cmdlets:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Ange regler för automatisk skalning för nodtyp/skalningsuppsättning för virtuell dator
Om klustret har flera nodtyper upprepar du detta för varje nodtyper/skalningsuppsättningar för virtuella datorer som du vill skala (in eller ut). Ta hänsyn till antalet noder som du måste ha innan du ställer in automatisk skalning. Det minsta antalet noder som du måste ha för den primära nodtypen avgörs av tillförlitlighetsnivån som du har valt. Läs mer om [tillförlitlighetsnivåer](service-fabric-cluster-capacity.md).

> [!NOTE]
> Skala ner den primära nodtypen till mindre än det minsta antalet gör klustret instabilt eller få ner det. Detta kan leda till dataförlust för dina program och för systemtjänsterna.
> 
> 

För närvarande drivs inte funktionen för automatisk skalning av de belastningar som dina program kan rapportera till Service Fabric. Så just nu den automatiska skalan du får är enbart drivs av prestandaräknare som avges av var och en av den virtuella datorn skala uppsättning instanser.  

Följ dessa instruktioner [för att ställa in automatisk skalning för varje virtuell datorskalauppsättning](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> I ett nedskalningsscenario, såvida inte nodtypen har en [hållbarhetsnivå][durability] för guld eller silver måste du anropa [cmdleten Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) med lämpligt nodnamn. För brons hållbarhet, det rekommenderas inte att skala ner mer än en nod i taget.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Lägg manuellt till virtuella datorer i en nodtyp/skaluppsättning för virtuell dator

När du skalar ut lägger du till fler instanser av virtuella datorer i skalningsuppsättningen. Dessa instanser blir de noder som används i Service Fabric. Service Fabric vet när fler instanser läggs till i skalningsuppsättningen (genom utskalning) och reagerar automatiskt. 

> [!NOTE]
> Lägga till virtuella datorer tar tid, så förvänta dig inte tilläggen vara ögonblicklig. Så planera att lägga till kapacitet i god tid, för att möjliggöra över 10 minuter innan den virtuella datorns kapacitet är tillgänglig för repliker / tjänstinstanser för att få placeras.
> 

### <a name="add-vms-using-a-template"></a>Lägga till virtuella datorer med hjälp av en mall
Följ exempel/instruktioner i [snabbstartsmallgalleriet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) om du vill ändra antalet virtuella datorer i varje nodtyp. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Lägga till virtuella datorer med PowerShell- eller CLI-kommandon
Följande kod hämtar en skalningsuppsättning efter namn och ökar **kapaciteten** för skalningsuppsättningen med 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Den här koden anger värdet 6 för kapaciteten.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Ta bort virtuella datorer manuellt från en nodtyp/skaluppsättning för virtuell dator
När du skalar i en nodtyp tar du bort VM-instanser från skalningsuppsättningen. Om nodtypen är bronshållbarhetsnivå är Service Fabric omedveten om vad som har hänt och rapporterar att en nod har försvunnit. Service Fabric rapporterar ett feltillstånd för klustret. För att förhindra det dåliga tillståndet måste du uttryckligen ta bort noden från klustret och ta bort nodtillståndet.

Tjänstinfrastruktursystemtjänsterna körs i den primära nodtypen i klustret. Skala aldrig ned antalet instanser till mindre än vad [tillförlitlighetsnivån](service-fabric-cluster-capacity.md) garanterar när den primära noden skalas ned. 
 
För en tillståndskänslig tjänst behöver du ett visst antal noder för att alltid vara upp för att upprätthålla tillgänglighet och bevara tillståndet för din tjänst. På ett minimum behöver du antalet noder som är lika med antalet målreplikuppsättningar för partitionen/tjänsten.

### <a name="remove-the-service-fabric-node"></a>Ta bort Service Fabric-noden

Stegen för att manuellt ta bort nodtillstånd gäller *Bronze* endast för nodtyper med en bronshållbarhetsnivå.  För *Silver-* och Gold-hållbarhetsnivån görs dessa steg automatiskt av plattformen. *Gold* Mer information om hållbarhet finns i [Kapacitetsplanering för Service Fabric-kluster][durability].

Om du vill hålla noderna i klustret jämnt fördelade över uppgraderings- och feldomäner och därmed möjliggöra jämn användning av dem, bör den senast skapade noden tas bort först. Med andra ord bör noderna tas bort i omvänd ordning mot hur de skapades. Den senast skapade noden är den som har den största egenskapsvärdet för `virtual machine scale set InstanceId`. Kodexemplen nedan returnerar den senast skapade noden.

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```shell
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Service Fabric-klustret måste informeras om att noden ska tas bort. Du måste göra tre saker:

1. Inaktivera noden så att den inte längre är en replik av data.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. Stoppa noden så att Service Fabric-körningen får en ren avstängning och appen får en avslutningsbegäran.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. Ta bort noden från klustret.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

När du har utfört dessa steg för noden kan du ta bort den från skalningsuppsättningen. Om du har någon annan hållbarhetsnivå än [Brons][durability] görs dessa steg åt dig när instansen av skalningsuppsättningen tas bort.

Följande kodblock hämtar den senaste skapade noden och inaktiverar, stoppar och tar bort noden från klustret.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

I **sfctl**-koden nedan används följande kommando för att hämta **node-name**-värdet för den nod som skapats senast: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```shell
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Använd följande **sfctl**-frågor för att kontrollera status för varje steg
>
> **Kontrollera avaktiveringsstatus**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Kontrollera stoppstatus**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>Skala in skalningsuppsättningen

Nu när Service Fabric-noden har tagits bort från klustret kan VM-skalningsuppsättningen skalas in. I exemplet nedan minskas skalningsuppsättningskapaciteten med 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Den här koden anger värdet 5 för kapaciteten.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Beteenden som du kan observera i Service Fabric Explorer
När du skalar upp ett kluster kommer Service Fabric Explorer att återspegla antalet noder (förekomster av skalningsuppsättningar för virtuella datorer) som ingår i klustret.  Men när du skalar ett kluster nedåt ser du den borttagna noden/VM-instansen som visas i ett feltillstånd om du inte anropar [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) med lämpligt nodnamn.   

Här är förklaringen till detta beteende.

Noderna som anges i Service Fabric Explorer är en återspegling av vad Service Fabric-systemtjänsterna (FM specifikt) vet om antalet noder som klustret hade/har. När du skalar den virtuella datorn skalan anges, den virtuella datorn togs bort men FM-systemtjänsten fortfarande tror att noden (som mappades till den virtuella datorn som togs bort) kommer tillbaka. Så Service Fabric Explorer fortsätter att visa den noden (även om hälsotillståndet kan vara fel eller okänt).

För att se till att en nod tas bort när en virtuell dator tas bort har du två alternativ:

1. Välj en hållbarhetsnivå för guld eller silver för nodtyperna i klustret, vilket ger dig infrastrukturintegreringen. Som sedan automatiskt tar bort noderna från vårt systemtjänster (FM) tillstånd när du skalar ner.
Se [information om hållbarhetsnivåer här](service-fabric-cluster-capacity.md)

2. När VM-instansen har skalats ned måste du anropa [cmdleten Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Service Fabric-kluster kräver att ett visst antal noder alltid ska vara uppe för att upprätthålla tillgänglighet och bevara tillstånd - kallat "upprätthålla kvorum". Så det är vanligtvis osäkert att stänga av alla datorer i klustret om du inte först har utfört en [fullständig säkerhetskopia av ditt tillstånd](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Nästa steg
Läs nedan om du också vill lära dig mer om hur du planerar klusterkapacitet, uppgraderar ett kluster och partitioneringstjänster:

* [Planera klusterkapaciteten](service-fabric-cluster-capacity.md)
* [Klusteruppgraderingar](service-fabric-cluster-upgrade.md)
* [Partitionens tillståndskänsliga tjänster för maximal skala](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
