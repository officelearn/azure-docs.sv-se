---
title: Skala ett Service Fabric-kluster in eller ut | Microsoft Docs
description: Skala ett Service Fabric-klustret in eller ut för att matcha begäran genom att ange regler för automatisk skalning för varje nod typ/virtual machine scale Sets. Lägga till eller ta bort noder till ett Service Fabric-kluster
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: aeb76f63-7303-4753-9c64-46146340b83d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2019
ms.author: aljo
ms.openlocfilehash: 4c3a9f00ed92194c4f81ab44cb9ca86d4a85fea1
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224354"
---
# <a name="scale-a-cluster-in-or-out"></a>Skala in eller ut ett kluster

> [!WARNING]
> Läs det här avsnittet innan du skalar

Skala beräkningsresurser till källan arbetsbelastning för ditt program kräver avsiktlig planering, nästan alltid tar längre tid än en timme att slutföra för en produktionsmiljö och kräver att du kan läsa mer arbetsbelastning affärskontexten; i själva verket om du aldrig har gjort den här aktiviteten innan, bör du börja med att läsa och förstå [Service Fabric-kluster kapacitetsplanering](service-fabric-cluster-capacity.md), innan du fortsätter med resten av det här dokumentet. Den här rekommendationen är att undvika oväntade LiveSite problem och vi rekommenderar också att du har testa de åtgärder som du vill utföra mot en icke-produktionsmiljö. Du kan när som helst [rapportera produktionsproblem eller begära betald support för Azure](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). För tekniker som allokerats för att utföra dessa åtgärder som har rätt kontext, i den här artikeln beskriver skalningsåtgärder, men du måste bestämma och förstå vilka åtgärder som är lämpliga för ditt användningsområde; till exempel vilka resurser för att skala (processor, lagring, minne), vilken riktning skala (vågrätt eller lodrätt) och vilka åtgärder att utföra (resursmall distribution, Portal, PowerShell/CLI).

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Skala ett Service Fabric-kluster in eller ut med hjälp av regler för automatisk skalning eller manuellt
Virtual machine scale sets är en Azure-beräkningsresurs som du kan använda för att distribuera och hantera en uppsättning virtuella datorer som en uppsättning. Varje nodtyp som definieras i Service Fabric-kluster har ställts in som en separat VM-skalningsuppsättning. Varje nodtyp skalas sedan in eller ut oberoende av varandra, ha olika portar öppna och ha olika kapacitet. Läs mer om den i den [Service Fabric-nodtyper](service-fabric-cluster-nodetypes.md) dokumentet. Eftersom Service Fabric-nodtyper i klustret består av VM-skalningsuppsättningar i serverdelen, behöver du konfigurera regler för automatisk skalning för varje nod typ/virtual machine scale Sets.

> [!NOTE]
> Prenumerationen måste ha tillräckligt många kärnor du lägger till de nya virtuella datorerna som tillsammans bildar det här klustret. Det finns ingen Modellvalidering, så att du får tid distributionsfel, om någon av kvotgränserna uppnår. En enda nodtyp kan också inte bara överstiga 100 noder per VMSS. Du kan behöva lägga till VMSS: er för att få den riktade skalan och automatisk skalning kan inte automagically lägga till VMSS'S. Lägga till VMSSS på plats till ett aktivt kluster är en utmaning och ofta detta leder användare etablera nya kluster med lämpliga nodtyperna etablerats vid tidpunkten för skapandet; [planera klusterkapacitet](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) därefter. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Välj noden typ/virtuell datorskalning som anger att skala
Du är för närvarande inte kan ange regler för automatisk skalning för VM scale sets med hjälp av portalen så kan vi använda Azure PowerShell (1.0 +) lista över typer noden och sedan lägga till regler för automatisk skalning till dem om du vill skapa ett Service Fabric-kluster.

Om du vill hämta listan över virtuella datorns skaluppsättning som utgör ditt kluster, kör du följande cmdlets:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Ange regler för automatisk skalning för noden typ/virtual machine scale Sets
Om klustret har flera nodtyper, upprepar du detta för varje nod-typer/virtuell datorskalning anger att du vill skala (in eller ut). Ta hänsyn till antalet noder som du måste ha innan du ställer in automatisk skalning. Det minsta antalet noder som du måste ha för den primära nodtypen avgörs av tillförlitlighetsnivån som du har valt. Läs mer om [tillförlitlighetsnivåer](service-fabric-cluster-capacity.md).

> [!NOTE]
> Skala ned den primära noden skriver till mindre än det minsta antalet gör klustret instabil eller påverkar den. Detta kan resultera i dataförlust för dina program och för systemtjänster.
> 
> 

Funktionen för automatisk skalning är för närvarande inte styrs av belastning som dina program kan ska rapportera till Service Fabric. Så just nu Autoskala du får helt och hållet drivs av prestandaräknare som genereras av den virtuella datorn skaluppsättningsinstanser.  

Följ de här instruktionerna [att ställa in automatisk skalning för varje VM-skalningsuppsättning](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> I en skala ned scenario, om inte din nodtyp har en [hållbarhetsnivå] [ durability] guld eller Silver måste du anropa den [cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) med den lämplig nodnamnet. För hållbarhet Brons rekommenderar vi inte för att skala ned mer än en nod i taget.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Lägga till virtuella datorer i en nod-skalningsuppsättning för typen/virtuella datorer manuellt

När du skalar ut lägger du till fler instanser av virtuella datorer i skalningsuppsättningen. Dessa instanser blir de noder som används i Service Fabric. Service Fabric vet när fler instanser läggs till i skalningsuppsättningen (genom utskalning) och reagerar automatiskt. 

> [!NOTE]
> Att lägga till virtuella datorer tar tid, så du inte räknar med tillägg till direkt. Planera så att lägga till kapacitet i förväg, att tillåta mer än 10 minuter innan den VM-kapaciteten är tillgänglig för repliker/tjänstinstanser att placeras.
> 

### <a name="add-vms-using-a-template"></a>Lägg till virtuella datorer med hjälp av en mall
I anvisningarna sample/i den [Snabbstart mallgalleriet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) att ändra antalet virtuella datorer i varje nodtyp. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Lägg till virtuella datorer med hjälp av PowerShell eller CLI-kommandon
Följande kod hämtar en skalningsuppsättning efter namn och ökar **kapaciteten** för skalningsuppsättningen med 1.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzureRmVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Den här koden anger värdet 6 för kapaciteten.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Ta manuellt bort virtuella datorer från en nod typ/virtual machine scale Sets
När du skalar in en nodtyp tar bort du VM-instanser från skalningsuppsättningen. Om nodtyp är Brons hållbarhetsnivå, är Service Fabric inte medveten om vad har hänt och rapporterar att en nod har gått förlorad. Service Fabric rapporterar ett feltillstånd för klustret. Om du vill förhindra att feltillståndet, måste du uttryckligen ta bort noden från klustret och ta bort node-tillstånd.

Service fabric-systemtjänster kör i den primära nodtypen i klustret. Vid Nedskalning den primära nodtypen aldrig skala ned antalet instanser till färre än vad den [tillförlitlighetsnivån](service-fabric-cluster-capacity.md) kräver. 
 
För en tillståndskänslig tjänst behöver du ett visst antal noder som ska vara alltid upp till att bibehålla tillgänglighet och bevara tillståndet för din tjänst. Vid mycket åtminstone behöver du antalet noder lika target set replikantalet för partitionen/tjänsten.

### <a name="remove-the-service-fabric-node"></a>Ta bort Service Fabric-noden

Stegen för att manuellt ta bort Nodtillstånd gäller endast för nodtyper med en *Brons* hållbarhetsnivå.  För *Silver* och *guld* hållbarhetsnivå, de här stegen utförs automatiskt av plattformen. Mer information om hållbarhet finns i [Kapacitetsplanering för Service Fabric-kluster][durability].

Om du vill hålla noderna i klustret jämnt fördelade över uppgraderings- och feldomäner och därmed möjliggöra jämn användning av dem, bör den senast skapade noden tas bort först. Med andra ord bör noderna tas bort i omvänd ordning mot hur de skapades. Den senast skapade noden är den som har den största egenskapsvärdet för `virtual machine scale set InstanceId`. Kodexemplen nedan returnerar den senast skapade noden.

```powershell
Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1
```

```azurecli
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

```azurecli
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
> **Kontrollera inaktiveringsstatus**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Kontrollera stoppstatus**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>Skala in skalningsuppsättningen

Nu när Service Fabric-noden har tagits bort från klustret kan VM-skalningsuppsättningen skalas in. I exemplet nedan minskas skalningsuppsättningskapaciteten med 1.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Den här koden anger värdet 5 för kapaciteten.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Beteenden som du kan se i Service Fabric Explorer
När du skalar upp ett kluster visas antalet noder (VM-skalningsuppsättningen instanser) som ingår i klustret med Service Fabric Explorer.  Men när du skalar ett kluster problem kan du ser den borttagna nod/VM-instansen som visas i ett feltillstånd, såvida inte du anropar [Remove-ServiceFabricNodeState cmd](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) med namnet på lämplig noden.   

Här är en förklaring för det här beteendet.

Noderna visas i Service Fabric Explorer är en avbildning av vilka de Service Fabric-systemtjänsterna (FM specifikt) känner antalet noder i klustret hade/har. När du skalar skalningsuppsättning för virtuell dator den virtuella datorn togs bort men FM systemtjänsten fortfarande tror att noden (som har kopplats till den virtuella datorn som har tagits bort) kommer tillbaka. Service Fabric Explorer fortsätter så att visa noden (även om hälsotillståndet kan vara fel eller okänd).

För att se till att en nod tas bort när en virtuell dator tas bort, har du två alternativ:

1. Välj hållbarhet nivån guld eller Silver för nodtyperna i klustret, vilket ger dig infrastruktur-integrering. Som sedan bort noderna från våra tjänster (FM) systemtillstånd när du skalar.
Referera till [information om hållbarhet nivåer här](service-fabric-cluster-capacity.md)

2. När VM-instansen har skalats, måste du anropa den [cmdlet Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Service Fabric-kluster kräver ett visst antal noder att vara igång hela tiden för att bibehålla tillgänglighet och bevara tillstånd - kallas ”upprätthålla kvorum”. Därför är det oftast säkert att Stäng alla datorer i klustret, såvida inte du först har utfört en [fullständig säkerhetskopiering av din delstat](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Nästa steg
Läs följande för att också lära dig om planera klusterkapacitet, uppgradera ett kluster och partitionering tjänster:

* [Planera din klusterkapacitet](service-fabric-cluster-capacity.md)
* [Klusteruppgradering](service-fabric-cluster-upgrade.md)
* [Partition tillståndskänsliga tjänster för maximal skala](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
