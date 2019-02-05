---
title: Ändra en Azure VM-skalningsuppsättning | Microsoft Docs
description: 'Lär dig hur du ändrar och uppdatera en Azure VM-skalningsuppsättning med REST API: er, Azure PowerShell och Azure CLI'
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
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
ms.author: manayar
ms.openlocfilehash: c0c9554a6c8868a8aeb90947dbbb0e251e42733f
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55733237"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Ändra en VM-skalningsuppsättning
Du kan behöva ändra eller uppdatera din skalningsuppsättning för virtuella datorer under hela livscykeln för programmen. De här uppdateringarna kan omfatta uppdatera konfigurationen för skaluppsättningen eller ändra programkonfigurationen. Den här artikeln beskriver hur du ändrar en befintlig skalningsuppsättning med REST API: er, Azure PowerShell eller Azure CLI.

## <a name="fundamental-concepts"></a>Grundläggande begrepp

### <a name="the-scale-set-model"></a>Skalningsuppsättningsmodell
En skalningsuppsättning har en ”skalningsuppsättningsmodell” som samlar in den *önskade* status skalans inställd som helhet. Om du vill fråga modellen för en skalningsuppsättning, kan du använda den 

- REST-API [beräkning/virtualmachinescalesets/hämta](/rest/api/compute/virtualmachinescalesets/get) på följande sätt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Azure PowerShell med [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss):

    ```powershell
    Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Azure CLI med [az vmss show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Du kan också använda [resources.azure.com](https://resources.azure.com) eller språkspecifika [Azure SDK: er](https://azure.microsoft.com/downloads/).

Hur utdata är beroende av de alternativ som du anger i kommandot. I följande exempel visas komprimerade exempel på utdata från Azure CLI:

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

Dessa egenskaper gäller i skalningsuppsättningen som helhet.


### <a name="the-scale-set-instance-view"></a>Instansvy för skalningsuppsättningen
En skalningsuppsättning också har en ”skalningsuppsättningens datorinstans Visa” som samlar in aktuellt *runtime* status skalans inställd som helhet. Om du vill fråga instansvyn för en skalningsuppsättning, kan du använda:

- REST-API [beräkning/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) på följande sätt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell med [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss):

    ```powershell
    Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Azure CLI med [az vmss get-instance-view](/cli/azure/vmss):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Du kan också använda [resources.azure.com](https://resources.azure.com) eller språkspecifika [Azure SDK: er](https://azure.microsoft.com/downloads/)

Hur utdata är beroende av de alternativ som du anger i kommandot. I följande exempel visas komprimerade exempel på utdata från Azure CLI:

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

Dessa egenskaper innehåller en sammanfattning av det aktuella runtime-tillståndet för de virtuella datorerna i skalningsuppsättningen, till exempel status för tillägg som tillämpas på skalningsuppsättningen.


### <a name="the-scale-set-vm-model-view"></a>Skaluppsättningen VM modellvy
Liknande hur en skalningsuppsättning har en modellvy varje virtuell datorinstans i skalningsuppsättningen har en egen modellvy. Om du vill fråga modellvy för specifika Virtuella datorinstanser i en skalningsuppsättning, kan du använda:

- REST-API [beräkning/virtualmachinescalesetvms/hämta](/rest/api/compute/virtualmachinescalesetvms/get) på följande sätt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Azure PowerShell med [Get-AzureRmVmssVm](/powershell/module/azurerm.compute/get-azurermvmssvm):

    ```powershell
    Get-AzureRmVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI med [az vmss show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Du kan också använda [resources.azure.com](https://resources.azure.com) eller [Azure SDK: er](https://azure.microsoft.com/downloads/).

Hur utdata är beroende av de alternativ som du anger i kommandot. I följande exempel visas komprimerade exempel på utdata från Azure CLI:

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

Dessa egenskaper beskriver konfigurationen av en VM-instans i en skalningsuppsättning, inte konfigurationen av skaluppsättningen som helhet. Skalningsuppsättningsmodell har exempelvis `overprovision` som en egenskap, men inte av modellen för en VM-instans i en skalningsuppsättning. Den här skillnaden beror på att skaffa stora resurser är en egenskap för skalningsuppsättningen som en hel, inte för enskilda VM-instanser i skalningsuppsättningen (Läs mer om överetablering [designöverväganden för skalningsuppsättningar](virtual-machine-scale-sets-design-overview.md#overprovisioning)).


### <a name="the-scale-set-vm-instance-view"></a>Den instansvyn för VM-skalningsuppsättning
Liknande hur en skalningsuppsättning har en Instansvy för varje virtuell datorinstans i skalningsuppsättningen har en egen instansvyn. Om du vill fråga instansvyn för en specifik VM-instans i en skalningsuppsättning, kan du använda:

- REST-API [beräkning/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) på följande sätt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell med [Get-AzureRmVmssVm](/powershell/module/azurerm.compute/get-azurermvmssvm):

    ```powershell
    Get-AzureRmVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Azure CLI med [az vmss get-instance-view](/cli/azure/vmss)

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Du kan också använda [resources.azure.com](https://resources.azure.com) eller [Azure SDK: er](https://azure.microsoft.com/downloads/)

Hur utdata är beroende av de alternativ som du anger i kommandot. I följande exempel visas komprimerade exempel på utdata från Azure CLI:

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

Dessa egenskaper beskriver det aktuella runtime-tillståndet för en VM-instans i en skalningsuppsättning som innehåller några tillägg som tillämpas på skalningsuppsättningen.


## <a name="how-to-update-global-scale-set-properties"></a>Så här uppdaterar du global skala ange egenskaper
Om du vill uppdatera globala skalningsuppsättningsegenskapen, måste du uppdatera egenskapen i modellen i skalningsuppsättningen. Du kan göra den här uppdateringen via:

- REST-API [beräkning/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) på följande sätt:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Du kan distribuera en Resource Manager-mall med egenskaper från REST-API för att uppdatera ange egenskaper för global skala.

- Azure PowerShell med [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss):

    ```powershell
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Azure CLI med [az vmss uppdatera](/cli/azure/vmss):
    - Ändra en egenskap:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Att lägga till ett objekt i en lista-egenskap i en skalningsuppsättning: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Ta bort ett objekt från en lista-egenskap i en skalningsuppsättning: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Om du tidigare har distribuerat skalningsuppsättningen med den `az vmss create` kommandot, kan du köra den `az vmss create` kommandot igen för att uppdatera skalningsuppsättningen. Se till att alla egenskaper i den `az vmss create` kommandot är samma som tidigare förutom de egenskaper som du vill ändra.

- Du kan också använda [resources.azure.com](https://resources.azure.com) eller [Azure SDK: er](https://azure.microsoft.com/downloads/).

När skalningsuppsättningsmodell har uppdaterats, gäller den nya konfigurationen för nya virtuella datorer skapas i skalningsuppsättningen. Dock måste modeller för befintliga virtuella datorer i skalningsuppsättningen fortfarande framställas uppdaterade med den senaste övergripande skalningsuppsättningen. I modellen för varje virtuell dator en boolesk egenskap kallas `latestModelApplied` -värde som anger om den virtuella datorn är uppdaterade med den senaste övergripande skalningsuppsättningsmodell (`true` innebär att den virtuella datorn är uppdaterade med den senaste modellen).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Hur du hanterar virtuella datorer uppdaterade med den senaste modellen för skalningsuppsättningen
Skalningsuppsättningar har en ”uppgraderingsprincip” som avgör hur virtuella datorer ansluts uppdaterade med den senaste modellen för skalningsuppsättningen. Det finns tre lägen för uppgraderingsprincipen:

- **Automatisk** – i det här läget skalningsuppsättningen ger inga garantier om i vilken ordning av virtuella datorer som tas. Skalningsuppsättningen kan ta bort alla virtuella datorer på samma gång. 
- **Löpande** – i det här läget skalningsuppsättningen samlar du uppdateringen i batchar med ett valfritt pausa mellan batchar.
- **Manuell** – i det här läget, när du uppdaterar skalningsuppsättningen, händer ingenting på befintliga virtuella datorer.
 
För att uppdatera befintliga virtuella datorer, måste du göra en ”manuell uppgradering” på varje befintlig virtuell dator. Du kan göra det här manuell uppgradering med:

- REST-API [beräkning/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) på följande sätt:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Azure PowerShell med [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance):
    
    ```powershell
    Update-AzureRmVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI med [az vmss update-instances](/cli/azure/vmss)

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Du kan också använda språkspecifika [Azure SDK: er](https://azure.microsoft.com/downloads/).

>[!NOTE]
> Service Fabric-kluster kan bara använda *automatisk* läge, men uppdateringen hanteras på olika sätt. Mer information finns i [ Service Fabric programuppgraderingar](../service-fabric/service-fabric-application-upgrade.md).

Det finns en typ av ändring av global skala ange egenskaper som inte följer uppgraderingsprincipen. Ändringar för skalningsuppsättningen OS-profil (till exempel administratörens användarnamn och lösenord) kan bara ändras i API-versionen *2017-12-01* eller senare. Dessa ändringar gäller endast för virtuella datorer som skapas när ändringen i skalan skalningsuppsättningsmodellen. För att lägga till befintliga virtuella datorer uppdaterade, måste du göra en ”reimage” för varje befintlig virtuell dator. Du kan göra det här reimage via:

- REST-API [beräkning/virtualmachinescalesets/reimage](/rest/api/compute/virtualmachinescalesets/reimage) på följande sätt:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Azure PowerShell med [Set-AzureRmVmssVm](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm):

    ```powershell
    Set-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Azure CLI med [az vmss reimage](https://docs.microsoft.com/cli/azure/vmss):

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Du kan också använda språkspecifika [Azure SDK: er](https://azure.microsoft.com/downloads/).


## <a name="properties-with-restrictions-on-modification"></a>Egenskaper med begränsningar för ändringar

### <a name="create-time-properties"></a>Skapa tid-egenskaper
Vissa egenskaper kan bara anges när du skapar skalningsuppsättningen. De här egenskaperna är:

- Tillgänglighetszoner
- Bildutgivare referens
- Erbjudande om referens
- Hanterade lagringskontotypen för OS-disk

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Egenskaper som kan bara ändras baserat på det aktuella värdet
Vissa egenskaper kan ändras, med undantag beroende på det aktuella värdet. De här egenskaperna är:

- **singlePlacementGroup** - om singlePlacementGroup är sant, den kan ändras till false. Men om singlePlacementGroup är falsk, den **får inte** ändras till true.
- **undernät** - undernätet för en skalningsuppsättning kan ändras så länge som det ursprungliga undernätet och det nya undernätet finns i samma virtuella nätverk.

### <a name="properties-that-require-deallocation-to-change"></a>Egenskaper som kräver flyttningen är att ändra
Vissa egenskaper kan bara ändras till vissa värden om de virtuella datorerna i skalningsuppsättningen frigörs. De här egenskaperna är:

- **SKU-namnet**– om den nya VM SKU inte stöds på den maskinvara som skalningsuppsättningen är för närvarande, måste du frigöra de virtuella datorerna i skaluppsättningen innan du ändrar SKU-namnet. Mer information finns i [ändra storlek på en Azure VM](../virtual-machines/windows/resize-vm.md).


## <a name="vm-specific-updates"></a>VM-specifika uppdateringar
Vissa ändringar kan tillämpas på specifika virtuella datorer i stället för global skala ange egenskaper. För närvarande den enda VM-specifik uppdatering som stöds är att koppla/koppla från datadiskar till/från virtuella datorer i skalningsuppsättningen. Den här funktionen är en förhandsversion. Mer information finns i den [Förhandsgranska dokumentation](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Scenarier

### <a name="application-updates"></a>Programuppdateringar
Om ett program har distribuerats till en skalningsuppsättning via tillägg, gör en uppdatering av tilläggskonfigurationen programmet för att uppdatera i enlighet med uppgraderingsprincipen. Till exempel om du har en ny version av ett skript köras i ett anpassat skripttillägg kan du uppdatera den *fileUris* egenskapen att peka mot det nya skriptet. I vissa fall kanske du vill framtvinga en uppdatering, även om tilläggskonfigurationen är oförändrad (till exempel du uppdaterat skriptet utan en ändring till URI: N för skriptet). I dessa fall kan du ändra den *forceUpdateTag* tvinga fram en uppdatering. Azure-plattformen tolka inte den här egenskapen. Om du ändrar värdet, finns det ingen effekt på hur tillägget körs. En ändring tvingar helt enkelt att köra tillägget. Mer information om den *forceUpdateTag*, finns i den [REST API-dokumentation för tilläggen](/rest/api/compute/virtualmachineextensions/createorupdate). Observera att den *forceUpdateTag* kan användas med alla tillägg, inte bara det anpassade skripttillägget.

Det är också vanligt för program som ska distribueras via en anpassad avbildning. Det här scenariot beskrivs i följande avsnitt.

### <a name="os-updates"></a>OS-uppdateringar
Om du använder Azure-plattformsavbildningar du kan uppdatera avbildningen genom att ändra den *imageReference* (Mer information finns i den [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> Med plattformsavbildningar är det vanligt att ange ”senaste” för referens Avbildningsversion. När du skapar, skala ut och återställa avbildningar, virtuella datorer skapas med den senaste tillgängliga versionen. Men det **inte** innebära att OS-avbildningen uppdateras automatiskt med tiden när den nya avbildningsversioner är tillgängliga. En separat funktion är för närvarande i förhandsversion som tillhandahåller automatiska uppgraderingar av Operativsystemet. Mer information finns i den [automatisk Operativsystemuppgradering dokumentation](virtual-machine-scale-sets-automatic-upgrade.md).

Om du använder anpassade avbildningar kan du uppdatera avbildningen genom att uppdatera den *imageReference* ID (Mer information finns i den [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Exempel

### <a name="update-the-os-image-for-your-scale-set"></a>Uppdatera OS-avbildningen för din skalningsuppsättning
Du kan ha en skalningsuppsättning som kör en äldre version av Ubuntu LTS, 16.04. Du vill uppdatera till en nyare version av Ubuntu LTS 16.04, till exempel version *16.04.201801090*. Bild referens versionsegenskapen är inte en del av en lista, så kan du direkt ändra dessa egenskaper med något av följande kommandon:

- Azure PowerShell med [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) på följande sätt:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Azure CLI med [az vmss uppdatera](/cli/azure/vmss):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

Alternativt kan du ändra avbildningen din skalningsuppsättning använder. Du kan till exempel vill uppdatera eller ändra en anpassad avbildning som används av din skalningsuppsättning. Du kan ändra den avbildning som din skalningsuppsättning använder genom att uppdatera egenskapen bild referens-ID: T. Image referens-ID-egenskapen är inte en del av en lista, så att du kan ändra den här egenskapen med något av följande kommandon direkt:

- Azure PowerShell med [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) på följande sätt:

    ```powershell
    Update-AzureRmVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- Azure CLI med [az vmss uppdatera](/cli/azure/vmss):

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Uppdatera belastningsutjämnaren för din skalningsuppsättning
Vi antar att du har en skalningsuppsättning med en Azure Load Balancer och du vill ersätta Azure Load Balancer med Azure Application Gateway. Load balancer och Application Gateway-egenskaperna för en skalningsuppsättning är en del av en lista, så du kan använda kommandon för att ta bort eller lägga till element i stället för att ändra egenskaperna direkt:

- Azure Powershell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
    $ipconf = New-AzureRmVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
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
> Kommandona förutsätter att det finns endast en IP-konfiguration och load balancer på skalningsuppsättningen. Om det finns flera kan du behöva använda en ListIndex än *0*.


## <a name="next-steps"></a>Nästa steg
Du kan också utföra vanliga administrationsuppgifter på skalningsuppsättningar med det [Azure CLI](virtual-machine-scale-sets-manage-cli.md) eller [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md).
