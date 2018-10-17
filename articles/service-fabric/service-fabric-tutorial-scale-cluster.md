---
title: Skala ut ett Service Fabric-kluster i Azure | Microsoft Docs
description: I den här självstudien får du lära dig att snabbt skala ut ett Service Fabric-kluster i Azure.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 010/01/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 1af4cdb361c1db378991201fc42f17dcbf67fe67
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238773"
---
# <a name="tutorial-scale-a-service-fabric-cluster-in-azure"></a>Självstudie: Skala ut ett Service Fabric-kluster i Azure

Det här är den andra delen i en kurs. I den här delen visas hur du skalar ut och in ditt befintliga kluster. När du är klar kommer du att veta hur du skalar ditt kluster och hur du rensar överblivna resurser.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Läser antalet klusternoder
> * Lägger till klusternoder (skalar ut)
> * Tar bort klusternoder (skalar in)

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eller [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) på Azure med hjälp av en mall
> * Skala in eller ut ett kluster
> * [uppgradera körningen för ett kluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Ta bort ett kluster](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar den här självstudien:

* om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installera [Azure Powershell-modulen version 4.1 eller senare](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) eller [Azure CLI](/cli/azure/install-azure-cli).
* Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eller [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) på Azure
* Om du distribuerar ett Windows-kluster måste du konfigurera en Windows-utvecklingsmiljö. Installera [Visual Studio 2017](http://www.visualstudio.com) och arbetsbelastningarna **Azure Development**, **ASP.NET och webbutveckling** samt **.NET Core plattformsoberoende utveckling**.  Konfigurera sedan en [.NET-utvecklingsmiljö](service-fabric-get-started.md).
* Om du distribuerar ett Linux-kluster måste du konfigurera en Java-utvecklingsmiljö på [Linux](service-fabric-get-started-linux.md) eller [MacOS](service-fabric-get-started-mac.md).  Installera [Service Fabric CLI](service-fabric-cli.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på ditt Azure-konto och välj din prenumeration innan du kör Azure-kommandon.

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="connect-to-the-cluster"></a>Anslut till klustret

För att slutföra den här delen av kursen måste du ansluta till både Service Fabric-klustret och VM-skalningsuppsättningen (som är värd för klustret). VM-skalningsuppsättningen är den Azure-resurs som är värd för Service Fabric på Azure.

När du ansluter till ett kluster kan du fråga den om information. Du kan använda klustret för att ta reda på vilka noder klustret känner till. I anslutningen till klustret i följande kod används samma certifikat som skapades i den första delen i den här serien. Se till att ange egna värden för variablerna `$endpoint` och `$thumbprint`.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint $thumbprint `
          -FindType FindByThumbprint -FindValue $thumbprint `
          -StoreLocation CurrentUser -StoreName My

Get-ServiceFabricClusterHealth
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Nu när du är ansluten kan använda du ett kommando för att hämta status för varje nod i klustret. I **PowerShell** använder du kommandot `Get-ServiceFabricClusterHealth` och i **sfctl** använder du kommandot `sfctl cluster select`.

## <a name="scale-out"></a>Skala ut

När du skalar ut lägger du till fler instanser av virtuella datorer i skalningsuppsättningen. Dessa instanser blir de noder som används i Service Fabric. Service Fabric vet när fler instanser läggs till i skalningsuppsättningen (genom utskalning) och reagerar automatiskt. Följande kod hämtar en skalningsuppsättning efter namn och ökar **kapaciteten** för skalningsuppsättningen med 1.

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

## <a name="scale-in"></a>Skala in

Inskalning fungerar på samma sätt som utskalning, men du använder ett lägre värde för **kapacitet**. När du skalar in skalningsuppsättningen tar du bort instanser för virtuella datorer från skalningsuppsättningen. Normalt är inte Service Fabric medveten om att en inskalning har skett utan tror att en nod har gått förlorad. Service Fabric rapporterar ett feltillstånd för klustret. För att förhindra att feltillståndet uppstår måste du informera Service Fabric om att det var väntat att noden försvinner.

### <a name="remove-the-service-fabric-node"></a>Ta bort Service Fabric-noden

> [!NOTE]
> Den här delen gäller endast hållbarhetsnivån *Brons*. Mer information om hållbarhet finns i [Kapacitetsplanering för Service Fabric-kluster][durability].

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

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Läser antalet klusternoder
> * Lägger till klusternoder (skalar ut)
> * Tar bort klusternoder (skalar in)

Fortsätt sedan till nästa självstudie för att lära dig hur du uppgraderar körningen för ett kluster.
> [!div class="nextstepaction"]
> [uppgradera körningen för ett kluster](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
