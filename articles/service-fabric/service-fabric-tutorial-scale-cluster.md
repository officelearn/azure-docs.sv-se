---
title: Skala ett Azure Service Fabric-kluster | Microsoft Docs
description: "Lär dig hur du snabbt skala ett Service Fabric-kluster."
services: service-fabric
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/24/2017
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: 63b4747164959b0e95f6d3f1908d1fd265589a98
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="scale-a-service-fabric-cluster"></a>Skala ett Service Fabric-kluster

Den här kursen ingår två av en serie och visar hur du kan skala ditt befintliga kluster och. När du är klar, kommer du att veta hur du skala ditt kluster och rensa alla blivit över resurser.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Noden för klustret läsningar
> * Lägga till noder i klustret (skalbar)
> * Tar bort klusternoder (skalan i)

I den här självstudiekursen serien lär du dig hur du:
> [!div class="checklist"]
> * Skapa en säker [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eller [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) på Azure med hjälp av en mall
> * Skala ett kluster in eller ut
> * [Uppgraderingen av körtiden för ett kluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Distribuera API Management med Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Krav
Innan du börjar den här kursen:
- Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Installera den [Azure Powershell Modulversion 4.1 eller högre](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) eller [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Skapa en säker [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) eller [Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md) på Azure
- Om du distribuerar ett Windows-kluster måste du konfigurera en Windows-utvecklingsmiljö. Installera [Visual Studio 2017](http://www.visualstudio.com) och **Azure-utveckling**, **ASP.NET och web development**, och **.NET Core plattformsoberoende development**arbetsbelastningar.  Ställa in en [.NET utvecklingsmiljö](service-fabric-get-started.md).
- Om du distribuerar en Linux-kluster måste du ställa in en Java-utvecklingsmiljö på [Linux](service-fabric-get-started-linux.md) eller [MacOS](service-fabric-get-started-mac.md).  Installera den [Service Fabric CLI](service-fabric-cli.md). 

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på ditt Azure-konto väljer din prenumeration innan du kan köra kommandon för Azure.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="connect-to-the-cluster"></a>Anslut till klustret

För att slutföra den här delen av kursen, behöver du ansluta till både Service Fabric-kluster och virtuella datorns skaluppsättning (som är värd för klustret). Virtuella datorns skaluppsättning är Azure-resurs som är värd för Service Fabric på Azure.

När du ansluter till ett kluster, kan den fråga information. Du kan använda i klustret för att lära dig om vilka noder i klustret är medveten om. Ansluta till klustret i följande kod använder samma certifikat som har skapats i den första delen av den här serien. Se till att ange den `$endpoint` och `$thumbprint` variabler som värdena.

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

Nu när du är ansluten kan använda du ett kommando för att hämta status för varje nod i klustret. PowerShell, Använd den `Get-ServiceFabricClusterHealth` kommando, och för **sfctl** använder den '' kommando.

## <a name="scale-out"></a>Skala ut

När du skalar upp du lägger till flera instanser av virtuell dator i skaluppsättning. Dessa instanser blir de noder som använder Service Fabric. Service Fabric vet när skaluppsättning har flera instanser som lagts till (genom att skala ut) och reagerar automatiskt. Följande kod hämtar en skala som anges av namn och ökar den **kapacitet** skalans in med 1.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Den här koden anger kapaciteten till 6.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="scale-in"></a>Skala i

Skalning i är samma som skala ut, förutom att du använder en lägre **kapacitet** värde. När du skalar i skaluppsättning du ta bort instanser för virtuella datorer från skaluppsättning. Normalt sett Service Fabric är inte medveten om vad som har hänt och den anser att en nod har tappats. Service Fabric rapporterar ett feltillstånd för klustret. Om du vill förhindra att felaktigt tillstånd, måste du informera service fabric som du förväntar dig noden försvinner.

### <a name="remove-the-service-fabric-node"></a>Ta bort service fabric-noden

> [!NOTE]
> Den här delen gäller bara den *Brons* hållbarhetsnivån. Läs mer om hållbarhet [kapacitetsplanering för Service Fabric-klustret][durability].

När du skalar i en skaluppsättning för virtuell dator, skaluppsättningen (i de flesta fall) tar du bort den virtuella datorinstans som skapades senast. Så behöver du hitta den motsvarande senast skapade service fabric-noden. Du kan hitta den sista noden genom att kontrollera det största `NodeInstanceId` egenskapsvärdet på service fabric-noder. Kodexempel under Sortera efter noden instansen och returnera information om instansen med det största id-värdet. 

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```azurecli
`sfctl node list --query "sort_by(items[*], &instanceId)[-1]"`
```

Service fabric-kluster behöver veta att den här noden kommer att tas bort. Det finns tre steg som du måste utföra:

1. Inaktivera noden så att den inte längre är en kopia för data.  
PowerShell:`Disable-ServiceFabricNode`  
sfcli:`sfctl node disable`

2. Stoppa noden så att service fabric runtime avslutas korrekt och din app hämtar en avsluta begäran.  
PowerShell:`Start-ServiceFabricNodeTransition -Stop`  
sfcli:`sfctl node transition --node-transition-type Stop`

2. Ta bort noden från klustret.  
PowerShell:`Remove-ServiceFabricNodeState`  
sfcli:`sfctl node remove-state`

När de här tre stegen har tillämpats på noden, kan tas bort från skaluppsättning. Om du använder någon hållbarhetsnivån förutom [Brons][durability], de här stegen är klar för dig när skaluppsättning instansen tas bort.

Hämtar den sista noden som skapade följande kodblock, inaktiverar, stoppar och tar bort noden från klustret.

```powershell
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1

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

I den **sfctl** code nedan, används följande kommando för att hämta den **nodnamnet** och **nod-instans-id** värden för noden senast skapade:`sfctl node list --query "sort_by(items[*], &instanceId)[-1].[instanceId,name]"`

```azurecli
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Använd följande **sfctl** frågor för att kontrollera status för varje steg
>
> **Kontrollera status för inaktivering**  
> `sfctl node list --query "sort_by(items[*], &instanceId)[-1].nodeDeactivationInfo"`
>
> **Kontrollera status för stoppa**  
> `sfctl node list --query "sort_by(items[*], &instanceId)[-1].isStopped"`
>


### <a name="scale-in-the-scale-set"></a>Skala i uppsättningen av skala

Nu när service fabric-noden har tagits bort från klustret, kan virtuella datorns skaluppsättning skalas i. I exemplet nedan minskas scale set kapacitet med 1.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Den här koden anger kapaciteten till 5.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Noden för klustret läsningar
> * Lägga till noder i klustret (skalbar)
> * Tar bort klusternoder (skalan i)


Gå sedan till följande kursen lär dig hur du uppgraderar körningen av ett kluster.
> [!div class="nextstepaction"]
> [Uppgraderingen av körtiden för ett kluster](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
