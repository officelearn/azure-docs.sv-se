---
title: Skala ett Service Fabric kluster in eller ut
description: Skala ett Service Fabric kluster i eller ut för att matcha efter frågan genom att ange regler för automatisk skalning för varje nodtyp/virtuell dators skalnings uppsättning. Lägga till eller ta bort noder till ett Service Fabric-kluster
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 42193ee06eda3f1d8c56b4db3251763b9dc52076
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774470"
---
# <a name="scale-a-cluster-in-or-out"></a>Skala in eller ut ett kluster

> [!WARNING]
> Läs det här avsnittet innan du skalar

Skala beräkningsresurser till källan arbetsbelastning för ditt program kräver avsiktlig planering, nästan alltid tar längre tid än en timme att slutföra för en produktionsmiljö och kräver att du kan läsa mer arbetsbelastning affärskontexten; i själva verket om du aldrig har gjort den här aktiviteten innan, bör du börja med att läsa och förstå [Service Fabric-kluster kapacitetsplanering](service-fabric-cluster-capacity.md), innan du fortsätter med resten av det här dokumentet. Den här rekommendationen är att undvika oväntade LiveSite problem och vi rekommenderar också att du har testa de åtgärder som du vill utföra mot en icke-produktionsmiljö. Du kan när som helst [rapportera produktionsproblem eller begära betald support för Azure](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). För tekniker som allokerats för att utföra dessa åtgärder som har rätt kontext, i den här artikeln beskriver skalningsåtgärder, men du måste bestämma och förstå vilka åtgärder som är lämpliga för ditt användningsområde; till exempel vilka resurser för att skala (processor, lagring, minne), vilken riktning skala (vågrätt eller lodrätt) och vilka åtgärder att utföra (resursmall distribution, Portal, PowerShell/CLI).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Skala ett Service Fabric-kluster in eller ut med hjälp av regler för automatisk skalning eller manuellt
Virtual machine scale sets är en Azure-beräkningsresurs som du kan använda för att distribuera och hantera en uppsättning virtuella datorer som en uppsättning. Varje nodtyp som definieras i Service Fabric-kluster har ställts in som en separat VM-skalningsuppsättning. Varje nodtyp skalas sedan in eller ut oberoende av varandra, ha olika portar öppna och ha olika kapacitet. Läs mer om det i dokumentet [Service Fabric Node types](service-fabric-cluster-nodetypes.md) . Eftersom Service Fabric-nodtypen i klustret består av virtuella datorers skalnings uppsättningar på Server delen måste du konfigurera regler för automatisk skalning för varje nodtyp/virtuell dators skalnings uppsättning.

> [!NOTE]
> Prenumerationen måste ha tillräckligt många kärnor du lägger till de nya virtuella datorerna som tillsammans bildar det här klustret. Det finns ingen Modellvalidering, så att du får tid distributionsfel, om någon av kvotgränserna uppnår. En enda nodtyp kan också inte bara överstiga 100 noder per VMSS. Du kan behöva lägga till VMSS: er för att få den riktade skalan och automatisk skalning kan inte automagically lägga till VMSS'S. Lägga till VMSSS på plats till ett aktivt kluster är en utmaning och ofta detta leder användare etablera nya kluster med lämpliga nodtyperna etablerats vid tidpunkten för skapandet; [planera klusterkapacitet](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity) därefter. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Välj noden typ/virtuell datorskalning som anger att skala
Du är för närvarande inte kan ange regler för automatisk skalning för VM scale sets med hjälp av portalen så kan vi använda Azure PowerShell (1.0 +) lista över typer noden och sedan lägga till regler för automatisk skalning till dem om du vill skapa ett Service Fabric-kluster.

Om du vill hämta listan över virtuella datorns skaluppsättning som utgör ditt kluster, kör du följande cmdlets:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Ange regler för automatisk skalning för den här nodtypen/skalnings uppsättningen för virtuell dator
Om klustret har flera nodtyper upprepar du detta för varje nodtyper/skalnings uppsättningar för virtuella datorer som du vill skala (in eller ut). Ta hänsyn till antalet noder som du måste ha innan du ställer in automatisk skalning. Det minsta antalet noder som du måste ha för den primära nodtypen avgörs av tillförlitlighetsnivån som du har valt. Läs mer om [tillförlitlighetsnivåer](service-fabric-cluster-capacity.md).

> [!NOTE]
> Skala ned den primära noden skriver till mindre än det minsta antalet gör klustret instabil eller påverkar den. Detta kan resultera i dataförlust för dina program och för systemtjänster.
> 
> 

Funktionen för automatisk skalning är för närvarande inte styrs av belastning som dina program kan ska rapportera till Service Fabric. Så just nu Autoskala du får helt och hållet drivs av prestandaräknare som genereras av den virtuella datorn skaluppsättningsinstanser.  

Följ de här anvisningarna [för att ställa in automatisk skalning för varje skalnings uppsättning för virtuella datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> I ett scenario med nedskalning, om inte nodtypen har en [hållbarhets nivå][durability] på guld eller silver, måste du anropa [Remove-ServiceFabricNodeState-cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate) : en med rätt nodnamn. För brons-hållbarhet rekommenderar vi inte att du skalar ned mer än en nod i taget.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Manuellt lägga till virtuella datorer till en nodtyp/skalnings uppsättning för virtuella datorer

När du skalar ut lägger du till fler instanser av virtuella datorer i skalningsuppsättningen. Dessa instanser blir de noder som används i Service Fabric. Service Fabric vet när fler instanser läggs till i skalningsuppsättningen (genom utskalning) och reagerar automatiskt. 

> [!NOTE]
> Det tar tid att lägga till virtuella datorer, så det är inte troligt att tilläggen tas i beaktande. Vi planerar att lägga till kapacitets rätt i förväg för att tillåta mer än 10 minuter innan VM-kapaciteten är tillgänglig för repliker/tjänst instanser som ska placeras.
> 

### <a name="add-vms-using-a-template"></a>Lägg till virtuella datorer med en mall
Följ exemplet/anvisningarna i [galleriet snabb starts mal len](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) för att ändra antalet virtuella datorer i varje nodtyp. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Lägg till virtuella datorer med PowerShell-eller CLI-kommandon
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

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Ta bort virtuella datorer manuellt från en nodtyp/skalnings uppsättning för virtuella datorer
När du skalar i en nodtyp tar du bort virtuella dator instanser från skalnings uppsättningen. Om nodtypen är brons hållbarhets nivå kan Service Fabric inte identifieras vad som har hänt och rapporterar att en nod saknas. Service Fabric rapporterar ett feltillstånd för klustret. För att förhindra att det är felaktigt, måste du uttryckligen ta bort noden från klustret och ta bort nodens tillstånd.

Service Fabric system Services körs i den primära nodtypen i klustret. Vid skalning av den primära nodtypen, skalar du aldrig ned antalet instanser till mindre än vad [Tillförlitlighets nivån](service-fabric-cluster-capacity.md) garanterar. 
 
För en tillståndskänslig tjänst behöver du ett visst antal noder som ska vara alltid upp till att bibehålla tillgänglighet och bevara tillståndet för din tjänst. Vid mycket åtminstone behöver du antalet noder lika target set replikantalet för partitionen/tjänsten.

### <a name="remove-the-service-fabric-node"></a>Ta bort Service Fabric-noden

Stegen för att ta bort Node-tillstånd manuellt gäller endast för nodtyper med en *brons* -hållbarhets nivå.  För *silver* -och *Gold* -hållbarhets nivån görs de här stegen automatiskt av plattformen. Mer information om hållbarhet finns i [Service Fabric kluster kapacitets planering][durability].

Om du vill hålla noderna i klustret jämnt fördelade över uppgraderings- och feldomäner och därmed möjliggöra jämn användning av dem, bör den senast skapade noden tas bort först. Med andra ord bör noderna tas bort i omvänd ordning mot hur de skapades. Den senast skapade noden är den som har den största egenskapsvärdet för `virtual machine scale set InstanceId`. Kodexemplen nedan returnerar den senast skapade noden.

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
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

När du har utfört dessa steg för noden kan du ta bort den från skalningsuppsättningen. Om du använder en hållbarhets nivå utöver [brons][durability]är de här stegen gjorda när du tar bort skalnings uppsättnings instansen.

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
