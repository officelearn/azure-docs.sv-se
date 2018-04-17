---
title: Ändra en skaluppsättning för virtuell dator i Azure | Microsoft Docs
description: 'Lär dig hur du ändrar och uppdatera en virtuell dator i Azure-skala med REST API: er, Azure PowerShell och Azure CLI 2.0'
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: negat
ms.openlocfilehash: bfbcf8ff3f24b69b49b9a2bd5d567e1ead57d974
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>Ändra en skaluppsättning för virtuell dator
Du kan behöva ändra eller uppdatera skaluppsättning för virtuell dator i hela livscykeln för dina program. Dessa uppdateringar kan innehålla hur du uppdaterar konfigurationen av skaluppsättning eller ändra konfigurationen för programmet. Den här artikeln beskriver hur du ändrar en befintlig skaluppsättningen med REST API: er, Azure PowerShell eller Azure CLI 2.0.

## <a name="fundamental-concepts"></a>grundläggande begrepp

### <a name="the-scale-set-model"></a>Modellen skaluppsättning
En skalningsuppsättning har en ”scale set modell” som samlar in den *önskade* ange tillståndet för skalan som helhet. Du kan använda för att fråga modellen för en skalningsuppsättning i 

- REST-API med [get-beräkning/virtualmachinescalesets](/rest/api/compute/virtualmachinescalesets/get) på följande sätt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Azure PowerShell med [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss):

    ```powershell
    Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Azure CLI 2.0 med [az vmss visa](/cli/azure/vmss#az_vmss_show):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Du kan också använda [resources.azure.com](https://resources.azure.com) eller språkspecifika [Azure SDK](https://azure.microsoft.com/downloads/).

Hur utdata beror på de alternativ som du anger i kommandot. I följande exempel visas komprimerad exempel på utdata från Azure CLI 2.0:

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

Dessa egenskaper gäller skaluppsättningen som helhet.


### <a name="the-scale-set-instance-view"></a>Instansvy för skaluppsättning
En skala som också har en ”scale set instans Visa” som samlar in aktuellt *runtime* ange tillståndet för skalan som helhet. Du kan använda om du vill fråga instansvyn för en skaluppsättning för:

- REST-API med [getinstanceview-beräkning/virtualmachinescalesets](/rest/api/compute/virtualmachinescalesets/getinstanceview) på följande sätt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell med [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss):

    ```powershell
    Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Azure CLI 2.0 med [az vmss get-instans view](/cli/azure/vmss#az_vmss_get_instance_view):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Du kan också använda [resources.azure.com](https://resources.azure.com) eller språkspecifika [Azure SDK](https://azure.microsoft.com/downloads/)

Hur utdata beror på de alternativ som du anger i kommandot. I följande exempel visas komprimerad exempel på utdata från Azure CLI 2.0:

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

Dessa egenskaper innehåller en översikt över det aktuella Körningstillståndet av de virtuella datorerna i skaluppsättning, till exempel status för tillägg som tillämpas på skaluppsättning.


### <a name="the-scale-set-vm-model-view"></a>Skalan ange VM modellvy
Liknande hur vyn modellen har i en skaluppsättning för varje virtuell dator i skaluppsättning för den har sin egen modellvy. Du kan använda för att fråga modellvyn för en skalningsuppsättning:

- REST-API med [get-beräkning/virtualmachinescalesetvms](/rest/api/compute/virtualmachinescalesetvms/get) på följande sätt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Azure PowerShell med [Get-AzureRmVmssVm](/powershell/module/azurerm.compute/get-azurermvmssvm):

    ```powershell
    Get-AzureRmVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI 2.0 med [az vmss visa](/cli/azure/vmss#az_vmss_show):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Du kan också använda [resources.azure.com](https://resources.azure.com) eller [Azure SDK](https://azure.microsoft.com/downloads/).

Hur utdata beror på de alternativ som du anger i kommandot. I följande exempel visas komprimerad exempel på utdata från Azure CLI 2.0:

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

De här egenskaperna beskriver konfigurationen av Virtuellt datorn, inte konfigurationen av skaluppsättningen som helhet. Till exempel scale set modellen har `overprovision` som en egenskap, men inte i modellen för en virtuell dator i en skaluppsättning. Den här skillnaden är eftersom överetablering är en egenskap för skaluppsättningen som en hela, inte enskilda virtuella datorer i skaluppsättning (Läs mer om överetablering [utformning för skalningsuppsättningar](virtual-machine-scale-sets-design-overview.md#overprovisioning)).


### <a name="the-scale-set-vm-instance-view"></a>VM-instansvyn skaluppsättning
Liknande hur en instansvyn har i en skaluppsättning för varje virtuell dator i skaluppsättning för har sin egen Instansvy. Du kan använda om du vill fråga instansvyn för en skaluppsättning för:

- REST-API med [getinstanceview-beräkning/virtualmachinescalesetvms](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) på följande sätt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell med [Get-AzureRmVmssVm](/powershell/module/azurerm.compute/get-azurermvmssvm):

    ```powershell
    Get-AzureRmVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Azure CLI 2.0 med [az vmss get-instans view](/cli/azure/vmss#az_vmss_get_instance_view)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Du kan också använda [resources.azure.com](https://resources.azure.com) eller [Azure SDK](https://azure.microsoft.com/downloads/)

Hur utdata beror på de alternativ som du anger i kommandot. I följande exempel visas komprimerad exempel på utdata från Azure CLI 2.0:

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

De här egenskaperna beskrivs det aktuella runtime-tillståndet för den Virtuella datorn, som innehåller alla tillägg som tillämpas på skaluppsättning.


## <a name="how-to-update-global-scale-set-properties"></a>Ange egenskaper för hur du uppdaterar global skala
Om du vill uppdatera en global skala den inställda egenskapen, måste du uppdatera egenskapen i scale set-modellen. Du kan göra den här uppdateringen via:

- REST-API med [createorupdate-beräkning/virtualmachinescalesets](/rest/api/compute/virtualmachinescalesets/createorupdate) på följande sätt:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Du kan distribuera en Resource Manager-mall med egenskaper från REST-API för att uppdatera egenskaper för global skala.

- Azure PowerShell med [uppdatering AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss):

    ```powershell
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Azure CLI 2.0 med [az vmss uppdatera](/cli/azure/vmss#az_vmss_update):
    - Ändra en egenskap:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Lägga till ett objekt i en lista över egenskap i en skaluppsättning: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Ta bort ett objekt från en lista över egenskap i en skaluppsättning: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Om du tidigare har distribuerat skaluppsättningen med den `az vmss create` kommandot kan du köra den `az vmss create` kommandot igen för att uppdatera skala. Se till att alla egenskaper i den `az vmss create` kommando är samma som tidigare förutom de egenskaper som du vill ändra.

- Du kan också använda [resources.azure.com](https://resources.azure.com) eller [Azure SDK](https://azure.microsoft.com/downloads/).

När scale set modellen uppdateras, gäller den nya konfigurationen för alla nya virtuella datorer skapas i skaluppsättning. Dock måste modeller för befintliga virtuella datorer i skaluppsättning fortfarande tas uppdaterade med den senaste övergripande skala modellen. I modellen för varje virtuell dator är en boolesk egenskap kallas `latestModelApplied` som anger om den virtuella datorn är uppdaterade med den senaste övergripande skala modellen (`true` innebär att den virtuella datorn har uppdaterats med den senaste modellen).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Hur du hanterar virtuella datorer uppdaterade med den senaste modellen med skalan
Skaluppsättningar har en ”princip för versionsuppgradering” som bestämmer hur virtuella datorer förs uppdaterade med den senaste modellen med skalan. De tre lägen för princip för versionsuppgradering är:

- **Automatisk** – i det här läget skaluppsättning ger inga garantier om i vilken ordning för virtuella datorer sättas. Skaluppsättning kan ta ned alla virtuella datorer på samma gång. 
- **Rullande** – i det här läget skaluppsättning samlar ut uppdateringen i batchar med ett valfritt paus mellan batchar.
- **Manuell** – i det här läget, när du uppdaterar modellen scale set händer ingenting till befintliga virtuella datorer.
 
Om du vill uppdatera befintliga virtuella datorer, måste du göra en ”manuell uppgradering” för varje befintlig virtuell dator. Du kan göra detta manuell uppgradering med:

- REST-API med [updateinstances-beräkning/virtualmachinescalesets](/rest/api/compute/virtualmachinescalesets/updateinstances) på följande sätt:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Azure PowerShell med [uppdatering AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance):
    
    ```powershell
    Update-AzureRmVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI 2.0 med [az vmss update-instanser](/cli/azure/vmss#az_vmss_update_instances)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Du kan också använda språkspecifika [Azure SDK](https://azure.microsoft.com/downloads/).

>[!NOTE]
> Service Fabric-kluster kan endast använda *automatisk* läge, men uppdateringen hanteras annorlunda. Mer information finns i [ Service Fabric programuppgraderingar](../service-fabric/service-fabric-application-upgrade.md).

Det finns en typ av ändring global skala ange egenskaper som inte följer principen för versionsuppgradering. Ändringar av skaluppsättning OS-profil (till exempel användarnamn och lösenord administratör) kan endast ändras i API-versionen *2017-12-01* eller senare. Ändringarna gäller endast för virtuella datorer skapas efter ändringen i skalan ange modell. Du måste göra ”återställa” varje befintlig virtuell dator för att göra befintliga virtuella datorer uppdaterade. Du kan göra detta avbildningsåterställning via:

- REST-API med [avbildningsåterställning-beräkning/virtualmachinescalesets](/rest/api/compute/virtualmachinescalesets/reimage) på följande sätt:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Azure PowerShell med [Set AzureRmVmssVm](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm):

    ```powershell
    Set-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Azure CLI 2.0 med [az vmss avbildningsåterställning](https://docs.microsoft.com/cli/azure/vmss#az_vmss_reimage):

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Du kan också använda språkspecifika [Azure SDK](https://azure.microsoft.com/downloads/).


## <a name="properties-with-restrictions-on-modification"></a>Egenskaper med begränsningar för ändringar

### <a name="create-time-properties"></a>Skapa egenskaper
Vissa egenskaper kan endast anges när du skapar skaluppsättning. Dessa egenskaper innehåller:

- Tillgänglighetszoner
- avbildningens referens utgivare
- bild referenserbjudande
- Hanterade lagringskontotypen för OS-disk

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Egenskaper som kan bara ändras baserat på det aktuella värdet
Vissa egenskaper kan ändras med undantag beroende på det aktuella värdet. Dessa egenskaper innehåller:

- **singlePlacementGroup** - om singlePlacementGroup är sant, den kan ändras till false. Men om singlePlacementGroup är false, den **får inte** ändras till true.
- **undernät** - undernätet i en skaluppsättning kan ändras så länge som det ursprungliga undernätet och det nya undernätet finns i samma virtuella nätverk.

### <a name="properties-that-require-deallocation-to-change"></a>Egenskaper som kräver flyttningen ändra
Vissa egenskaper kan bara ändras till vissa värden om de virtuella datorerna i skaluppsättning har frigjorts. Dessa egenskaper innehåller:

- **SKU namn**– om den nya VM SKU inte stöds på den maskinvara som skaluppsättning är för närvarande, måste du frigöra de virtuella datorerna i skaluppsättningen innan du ändrar SKU-namnet. Mer information finns i [så att ändra storlek på en Azure VM](../virtual-machines/windows/resize-vm.md).


## <a name="vm-specific-updates"></a>VM-specifika uppdateringar
Vissa ändringar kan tillämpas på specifika virtuella datorer i stället för global skala ange egenskaper. För närvarande den enda VM-specifik uppdatering som stöds är att koppla/frånkoppla datadiskar till/från virtuella datorer i skaluppsättning. Den här funktionen är i förhandsgranskningen. Mer information finns i [Förhandsgranska dokumentationen](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Scenarier

### <a name="application-updates"></a>Programuppdateringar
Om ett program distribueras till en skala som anges via tillägg, gör en uppdatering av tilläggets konfiguration programmet för att uppdatera i enlighet med principen för versionsuppgradering. Till exempel om du har en ny version av ett skript köras i tillägget för anpassat skript kan du uppdatera den *fileUris* egenskapen att peka mot det nya skriptet. I vissa fall kanske du vill framtvinga en uppdatering även om konfigurationen av tillägget är oförändrad (till exempel du uppdaterade skriptet utan en ändring till URI: N av skriptet). I dessa fall kan du ändra den *forceUpdateTag* tvinga fram en uppdatering. Azure-plattformen tolka inte den här egenskapen. Om du ändrar värdet, finns det ingen effekt på hur tillägget körs. En ändring tvingar bara tillägget igen. Mer information om den *forceUpdateTag*, finns det [REST API-dokumentation för tilläggen](/rest/api/compute/virtualmachineextensions/createorupdate). Observera att den *forceUpdateTag* kan användas med alla tillägg, inte bara tillägget för anpassat skript.

Det är vanligt för program som ska distribueras via en anpassad avbildning. Det här scenariot beskrivs i följande avsnitt.

### <a name="os-updates"></a>OS-uppdateringar
Om du använder Azure-plattformen bilder, kan du uppdatera bilden genom att ändra den *imageReference* (Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> Med plattformen bilder är det vanligt att ange ”senaste” för avbildning Referensversion. När du skapar skala ut och avbildningsåterställning, skapas virtuella datorer med den senaste tillgängliga versionen. Men den **inte** innebär att OS-avbildningen uppdateras automatiskt med tiden som släpps nya avbildningsversioner. En separat funktion är för närvarande under förhandsgranskning som tillhandahåller den automatiska OS uppgraderingar. Mer information finns i [automatiska uppgraderingar av OS-dokumentationen](virtual-machine-scale-sets-automatic-upgrade.md).

Om du använder anpassade avbildningar kan du uppdatera bilden genom att uppdatera den *imageReference* ID (Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Exempel

### <a name="update-the-os-image-for-your-scale-set"></a>Uppdatera operativsystemsavbildning för din skaluppsättning
Du kan ha en skalningsuppsättning som kör en gammal version av Ubuntu LTS 16.04. Du vill uppdatera till en nyare version av Ubuntu LTS 16.04, till exempel version *16.04.201801090*. Version för avbildningen referensegenskapen ingår inte i en lista så kan du direkt ändra dessa egenskaper med något av följande kommandon:

- Azure PowerShell med [uppdatering AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) på följande sätt:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Azure CLI 2.0 med [az vmss uppdatera](/cli/azure/vmss#az_vmss_update_instances):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Uppdatera belastningsutjämnaren för din skaluppsättning
Anta att du har en skala som anges med en Azure belastningsutjämnare och du vill ersätta belastningsutjämnaren Azure med en Azure Application Gateway. För belastningsutjämning och Programgateway egenskaperna för en skalningsuppsättning ingår i en lista, så du kan använda kommandon för att ta bort eller lägga till element i stället för att ändra egenskaperna direkt:

- Azure Powershell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the referencerence to the application gateway
    $ipconf = New-AzureRmVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- Azure CLI 2.0:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> Kommandona förutsätter att det finns endast en IP-konfiguration och Läs in belastningsutjämnaren på skaluppsättning. Om det finns flera, kan du behöva använda en ListIndex än *0*.


## <a name="next-steps"></a>Nästa steg
Du kan också utföra vanliga hanteringsuppgifter på med den [Azure CLI 2.0](virtual-machine-scale-sets-manage-cli.md) eller [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).
