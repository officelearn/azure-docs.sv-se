---
title: Ändra en azure-skalningsuppsättning för virtuella datorer
description: Lär dig hur du ändrar och uppdaterar en Azure-skalningsuppsättning för virtuella datorer med REST-API:erna, Azure PowerShell och Azure CLI
author: mimckitt
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimckitt
ms.openlocfilehash: 66fd656b5175547641150a048e57c978dc06d291
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476832"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Skapa en VM-skalningsuppsättning

Under programmens hela livscykel kan du behöva ändra eller uppdatera skalningsuppsättningen för den virtuella datorn. Dessa uppdateringar kan omfatta hur du uppdaterar konfigurationen av skalningsuppsättningen eller ändrar programkonfigurationen. I den här artikeln beskrivs hur du ändrar en befintlig skalningsuppsättning med REST-API:erna, Azure PowerShell eller Azure CLI.

## <a name="fundamental-concepts"></a>Grundläggande koncept

### <a name="the-scale-set-model"></a>Skalningsuppsättningsmodellen
En skalningsuppsättning har en "skaluppsättningsmodell" som fångar *önskat* tillstånd för skalningsuppsättningen som helhet. Om du vill fråga modellen efter en skalningsuppsättning kan du använda 

- REST API med [compute/virtualmachinescalesets/get](/rest/api/compute/virtualmachinescalesets/get) as follows:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Azure PowerShell med [Get-AzVmss:](/powershell/module/az.compute/get-azvmss)

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Azure CLI med [az vmss visar:](/cli/azure/vmss)

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Du kan också använda [resources.azure.com](https://resources.azure.com) eller de språkspecifika [Azure SDK:er](https://azure.microsoft.com/downloads/).

Den exakta presentationen av utdata beror på vilka alternativ du anger för kommandot. I följande exempel visas komprimerade exempelutdata från Azure CLI:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Dessa egenskaper gäller för skalningsuppsättningen som helhet.


### <a name="the-scale-set-instance-view"></a>Vyn skaluppsättningsförekomst
En skalningsuppsättning har också en "skalningsuppsättningsinstansvy" som fångar det aktuella *körningstillståndet* för skalningsuppsättningen som helhet. Om du vill fråga instansvyn efter en skalningsuppsättning kan du använda:

- REST API med [compute/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) enligt följande:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell med [Get-AzVmss:](/powershell/module/az.compute/get-azvmss)

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Azure CLI med [az vmss get-instance-view:](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Du kan också använda [resources.azure.com](https://resources.azure.com) eller de språkspecifika [Azure SDK:er](https://azure.microsoft.com/downloads/)

Den exakta presentationen av utdata beror på vilka alternativ du anger för kommandot. I följande exempel visas komprimerade exempelutdata från Azure CLI:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
}
```

Dessa egenskaper ger en sammanfattning av det aktuella körningstillståndet för de virtuella datorerna i skalningsuppsättningen, till exempel status för tillägg som tillämpas på skalningsuppsättningen.


### <a name="the-scale-set-vm-model-view"></a>Skalningsuppsättning VM-modellvyn
I likhet med hur en skalningsuppsättning har en modellvy har varje VM-instans i skalningsuppsättningen en egen modellvy. Om du vill fråga modellvyn efter en viss VM-instans i en skalningsuppsättning kan du använda:

- REST API med [compute/virtualmachinescalesetvms/get](/rest/api/compute/virtualmachinescalesetvms/get) enligt följande:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Azure PowerShell med [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI med [az vmss visar:](/cli/azure/vmss)

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Du kan också använda [resources.azure.com](https://resources.azure.com) eller [Azure SDK: er](https://azure.microsoft.com/downloads/).

Den exakta presentationen av utdata beror på vilka alternativ du anger för kommandot. I följande exempel visas komprimerade exempelutdata från Azure CLI:

```azurecli
$ az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Dessa egenskaper beskriver konfigurationen av en VM-instans inom en skalningsuppsättning, inte konfigurationen av skalningsuppsättningen som helhet. Skaluppsättningsmodellen har `overprovision` till exempel som egenskap, medan modellen för en VM-instans i en skalningsuppsättning inte gör det. Den här skillnaden beror på att överetablering är en egenskap för skalningsuppsättningen som helhet, inte enskilda VM-instanser i skalningsuppsättningen (för mer information om överetablering, se [Designöverväganden för skaluppsättningar](virtual-machine-scale-sets-design-overview.md#overprovisioning)).


### <a name="the-scale-set-vm-instance-view"></a>Vyn skala uppsättning VM-instans
På samma sätt som en skalningsuppsättning har en instansvy har varje VM-instans i skalningsuppsättningen en egen instansvy. Om du vill fråga instansvyn för en viss VM-instans i en skalningsuppsättning kan du använda:

- REST API med [compute/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) enligt följande:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell med [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Azure CLI med [az vmss get-instance-view](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Du kan också använda [resources.azure.com](https://resources.azure.com) eller [Azure SDK:er](https://azure.microsoft.com/downloads/)

Den exakta presentationen av utdata beror på vilka alternativ du anger för kommandot. I följande exempel visas komprimerade exempelutdata från Azure CLI:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
}
```

De här egenskaperna beskriver det aktuella körningstillståndet för en VM-instans i en skalningsuppsättning, som innehåller alla tillägg som tillämpas på skalningsuppsättningen.


## <a name="how-to-update-global-scale-set-properties"></a>Så här uppdaterar du globala skaluppsättningsegenskaper
Om du vill uppdatera en global skalningsuppsättningsegenskap måste du uppdatera egenskapen i skalningsuppsättningsmodellen. Du kan göra den här uppdateringen via:

- REST API med [compute/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) enligt följande:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Du kan distribuera en Resource Manager-mall med egenskaperna från REST API för att uppdatera globala skalningsuppsättningsegenskaper.

- Azure PowerShell med [Update-AzVmss:](/powershell/module/az.compute/update-azvmss)

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Azure CLI med [az vmss uppdatering:](/cli/azure/vmss)
    - Så här ändrar du en egenskap:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Så här lägger du till ett objekt i en listegenskap i en skalningsuppsättning: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Så här tar du bort ett objekt från en listegenskap i en skalningsuppsättning: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Om du tidigare har distribuerat skalningsuppsättningen `az vmss create` med kommandot kan du köra `az vmss create` kommandot igen för att uppdatera skalningsuppsättningen. Kontrollera att alla egenskaper `az vmss create` i kommandot är desamma som tidigare, förutom de egenskaper som du vill ändra.

- Du kan också använda [resources.azure.com](https://resources.azure.com) eller [Azure SDK: er](https://azure.microsoft.com/downloads/).

När skalningsuppsättningsmodellen har uppdaterats gäller den nya konfigurationen för alla nya virtuella datorer som skapas i skalningsuppsättningen. Modellerna för befintliga virtuella datorer i skalningsuppsättningen måste dock fortfarande uppdateras med den senaste övergripande skaluppsättningsmodellen. I modellen för varje virtuell dator `latestModelApplied` är en boolesk egenskap som kallas som anger om den`true` virtuella datorn är uppdaterad med den senaste övergripande skalningsuppsättningsmodellen ( innebär att den virtuella datorn är uppdaterad med den senaste modellen).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Så här gör du virtuella datorer uppdaterade med den senaste skaluppsättningsmodellen
Skalningsuppsättningar har en "uppgraderingsprincip" som avgör hur virtuella datorer ska tas upp till datum med den senaste skalningsuppsättningsmodellen. De tre lägena för uppgraderingsprincipen är:

- **Automatisk** - I det här läget ger skalningsuppsättningen inga garantier om ordningen på virtuella datorer som fälls. Skalningsuppsättningen kan ta ned alla virtuella datorer samtidigt. 
- **Rullande** - I det här läget distribuerar skalningsuppsättningen ut uppdateringen i batchar med en valfri paustid mellan batchar.
- **Manuell** - I det här läget, när du uppdaterar skalningsuppsättningsmodellen, händer ingenting med befintliga virtuella datorer.
 
Om du vill uppdatera befintliga virtuella datorer måste du göra en "manuell uppgradering" av varje befintlig virtuell dator. Du kan göra den här manuella uppgraderingen med:

- REST API med [compute/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) enligt följande:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Azure PowerShell med [Uppdatering-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance):
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI med [az vmss update-instances](/cli/azure/vmss)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Du kan också använda de språkspecifika [Azure SDK:er](https://azure.microsoft.com/downloads/).

>[!NOTE]
> Service Fabric-kluster kan bara använda *automatiskt* läge, men uppdateringen hanteras på olika sätt. Mer information finns i [Uppgraderingar av Service Fabric-programmet](../service-fabric/service-fabric-application-upgrade.md).

Det finns en typ av ändring av globala skalgruppsegenskaper som inte följer uppgraderingsprincipen. Ändringar i skalningsuppsättningen OS och Data diskprofil (till exempel administratörsanvändarnamn och lösenord) kan bara ändras i API-version *2017-12-01* eller senare. Dessa ändringar gäller endast för virtuella datorer som skapats efter ändringen i skalningsuppsättningsmodellen. Om du vill att befintliga virtuella datorer ska vara uppdaterade måste du göra en "ombildning" av varje befintlig virtuell dator. Du kan göra denna reimage via:

- REST API med [compute/virtualmachinescalesets/reimage](/rest/api/compute/virtualmachinescalesets/reimage) enligt följande:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Azure PowerShell med [Set-AzVmssVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm):

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Azure CLI med [az vmss reimage:](https://docs.microsoft.com/cli/azure/vmss)

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Du kan också använda de språkspecifika [Azure SDK:er](https://azure.microsoft.com/downloads/).


## <a name="properties-with-restrictions-on-modification"></a>Egenskaper med begränsningar för ändring

### <a name="create-time-properties"></a>Egenskaper för skapa tid
Vissa egenskaper kan bara anges när du skapar skalningsuppsättningen. Dessa egenskaper inkluderar:

- Tillgänglighetszoner
- Utgivare av bildreferens
- Bildreferenserbjudande
- Bildreferens sku
- Kontotyp för hanterad os-disklagring

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Egenskaper som bara kan ändras baserat på det aktuella värdet
Vissa egenskaper kan ändras, med undantag beroende på det aktuella värdet. Dessa egenskaper inkluderar:

- **singlePlacementGroup** - Om singlePlacementGroup är sant kan det ändras till false. Men om singlePlacementGroup är **falskt, kan** det inte ändras till sant.
- **undernät** - Undernätet i en skalningsuppsättning kan ändras så länge det ursprungliga undernätet och det nya undernätet finns i samma virtuella nätverk.

### <a name="properties-that-require-deallocation-to-change"></a>Egenskaper som kräver deallocation för att ändra
Vissa egenskaper kan bara ändras till vissa värden om de virtuella datorerna i skalningsuppsättningen är dislocated. Dessa egenskaper inkluderar:

- **SKU-namn**- Om den nya VM SKU inte stöds på maskinvaran skalningsuppsättningen är för närvarande på, måste du frigöra de virtuella datorerna i skalningsuppsättningen innan du ändrar SKU-namnet. Mer information finns i [hur du ändrar storlek på en Virtuell Azure.](../virtual-machines/windows/resize-vm.md)


## <a name="vm-specific-updates"></a>VM-specifika uppdateringar
Vissa ändringar kan tillämpas på specifika virtuella datorer i stället för egenskaperna för den globala skaluppsättningen. För närvarande är den enda VM-specifika uppdateringen som stöds att koppla/koppla bort datadiskar till/från virtuella datorer i skalningsuppsättningen. Den här funktionen är en förhandsversion. Mer information finns i [förhandsversionen](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Scenarier

### <a name="application-updates"></a>Programuppdateringar
Om ett program distribueras till en skalningsuppsättning via tillägg, gör en uppdatering av tilläggskonfigurationen att programmet uppdateras i enlighet med uppgraderingsprincipen. Om du till exempel har en ny version av ett skript som ska köras i ett anpassat skripttillägg kan du uppdatera egenskapen *fileUris* så att den pekar på det nya skriptet. I vissa fall kanske du vill tvinga fram en uppdatering även om tilläggskonfigurationen är oförändrad (du har till exempel uppdaterat skriptet utan att ändra skriptets URI). I dessa fall kan du ändra *forceUpdateTag för* att tvinga fram en uppdatering. Azure-plattformen tolkar inte den här egenskapen. Om du ändrar värdet påverkas inte hur tillägget körs. En förändring tvingar helt enkelt förlängningen att köra igen. Mer information om *forceUpdateTag*finns i [REST API-dokumentationen för tillägg](/rest/api/compute/virtualmachineextensions/createorupdate). Observera att *forceUpdateTag* kan användas med alla tillägg, inte bara det anpassade skripttillägget.

Det är också vanligt att program distribueras via en anpassad avbildning. Det här scenariot beskrivs i följande avsnitt.

### <a name="os-updates"></a>Os-uppdateringar
Om du använder Azure-plattformsavbildningar kan du uppdatera avbildningen genom att ändra *avbildningenReferens* (mer information finns i [REST API-dokumentationen](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> Med plattformsavbildningar är det vanligt att ange "senaste" för bildreferensversionen. När du skapar, skalar ut och imerar om skapas virtuella datorer med den senaste tillgängliga versionen. Det betyder dock **inte** att OS-avbildningen uppdateras automatiskt med tiden när nya bildversioner släpps. En separat funktion är för närvarande i förhandsgranskning som ger automatiska OS-uppgraderingar. Mer information finns i [dokumentationen för Automatiska os-uppgraderingar](virtual-machine-scale-sets-automatic-upgrade.md).

Om du använder anpassade bilder kan du uppdatera avbildningen genom att uppdatera *imageReference* ID (mer information finns i [REST API-dokumentationen](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Exempel

### <a name="update-the-os-image-for-your-scale-set"></a>Uppdatera OS-avbildningen för skalningsuppsättningen
Du kan ha en skalningsuppsättning som kör en gammal version av Ubuntu LTS 16.04. Du vill uppdatera till en nyare version av Ubuntu LTS 16.04, till exempel version *16.04.201801090*. Egenskapen image reference version är inte en del av en lista, så du kan direkt ändra dessa egenskaper med något av följande kommandon:

- Azure PowerShell med [Update-AzVmss](/powershell/module/az.compute/update-azvmss) enligt följande:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Azure CLI med [az vmss uppdatering:](/cli/azure/vmss)

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

Du kan också ändra bilden som skalningsuppsättningen använder. Du kanske till exempel vill uppdatera eller ändra en anpassad bild som används av skalningsuppsättningen. Du kan ändra bilden som skalningsuppsättningen använder genom att uppdatera egenskapen image reference ID. Egenskapen image reference ID ingår inte i en lista, så du kan ändra den här egenskapen direkt med något av följande kommandon:

- Azure PowerShell med [Update-AzVmss](/powershell/module/az.compute/update-azvmss) enligt följande:

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- Azure CLI med [az vmss uppdatering:](/cli/azure/vmss)

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Uppdatera belastningsutjämnaren för din skalningsuppsättning
Anta att du har en skalningsuppsättning med en Azure Load Balancer och vill ersätta Azure Load Balancer med en Azure Application Gateway. Egenskaperna belastningsutjämnare och programgateway för en skalningsuppsättning ingår i en lista, så du kan använda kommandona för att ta bort eller lägga till listelement i stället för att ändra egenskaperna direkt:

- Azure Powershell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
    $ipconf = New-AzVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- Azure CLI:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> Dessa kommandon förutsätter att det bara finns en IP-konfiguration och belastningsutjämnare på skalningsuppsättningen. Om det finns flera kan du behöva använda ett annat listindex än *0*.


## <a name="next-steps"></a>Nästa steg
Du kan också utföra vanliga hanteringsuppgifter i skalningsuppsättningar med [Azure CLI](virtual-machine-scale-sets-manage-cli.md) eller [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).
