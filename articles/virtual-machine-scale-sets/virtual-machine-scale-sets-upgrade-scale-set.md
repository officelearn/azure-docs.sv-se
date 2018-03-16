---
title: "Ändra en skaluppsättning för virtuell dator i Azure | Microsoft Docs"
description: "Ändra en skaluppsättning för virtuell Azure-dator"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: negat
ms.openlocfilehash: fcca912a8120a51d2f0a454ef0a6341cd5882015
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>Ändra en skaluppsättning för virtuell dator
Den här artikeln beskriver hur du ändrar en befintlig skaluppsättning för virtuell dator. Uppgifterna är hur du ändrar konfigurationen för skalan ange, hur du ändrar konfigurationen för de program som körs på skalan ange, hur du hanterar tillgänglighet med mera.

## <a name="fundamental-concepts"></a>grundläggande begrepp

### <a name="scale-set-model"></a>Skaluppsättning för modellen

En skalningsuppsättning har en modell som samlar in den *önskade* ange tillståndet för skalan som helhet. Du kan använda för att fråga modellen för en skalningsuppsättning:

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
   
  Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/get).

* PowerShell:

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}`
   
  Mer information finns i [PowerShell dokumentationen](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss).

* Azure CLI: 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName}` 
   
  Mer information finns i [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show).

Du kan också använda [resursutforskaren i Azure (förhandsversion)](https://resources.azure.com) eller [Azure SDK](https://azure.microsoft.com/downloads/) att fråga modellen för en skaluppsättning.

Hur utdata beror på de alternativ som du anger i kommandot. Här är exempel på utdata från Azure CLI:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
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
  .
  .
  .
}
```

Du kan se tillämpas dessa egenskaper i skaluppsättningen som helhet.



### <a name="scale-set-instance-view"></a>Instansvy för skaluppsättning

En skala som också har en instansvyn som samlar in aktuellt *runtime* ange tillståndet för skalan som helhet. Du kan använda om du vill fråga instansvyn för en skaluppsättning för:

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version={apiVersion}` 
   
  Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview).

* PowerShell: 

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceView` 
  
  Mer information finns i [PowerShell dokumentationen](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss).

* Azure CLI: 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName}` 
   
  Mer information finns i [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view).

Du kan också använda [resursutforskaren i Azure (förhandsversion)](https://resources.azure.com) eller [Azure SDK](https://azure.microsoft.com/downloads/) fråga instansvyn för en skaluppsättning.

Hur utdata beror på de alternativ som du anger i kommandot. Här är exempel på utdata från Azure CLI:

```
$ az vmss get-instance-view -g {resourceGroupName} -n {virtualMachineScaleSetName}
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
  .
  .
  .
}
```

Som du kan se innehåller dessa egenskaper en sammanfattning av det aktuella runtime-tillståndet för de virtuella datorerna i skaluppsättning. Sammanfattningen innehåller statusen för tillägg som tillämpas på skaluppsättningen (utelämnat planeringsaspekter).



### <a name="scale-set-vm-model-view"></a>Skaluppsättning för Virtuella modellvy

Liknande hur vyn modellen har i en skaluppsättning för varje virtuell dator i skaluppsättning för den har sin egen modellvy. Du kan använda för att fråga modellvyn för en skalningsuppsättning:

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}?api-version={apiVersion}` 
  
  Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/get).

* PowerShell: 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
  
  Mer information finns i [PowerShell dokumentationen](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm).

* Azure CLI: 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  Mer information finns i [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show).

Du kan också använda [resursutforskaren i Azure (förhandsversion)](https://resources.azure.com) eller [Azure SDK](https://azure.microsoft.com/downloads/) att fråga modellen för en virtuell dator i en skaluppsättning.

Hur utdata beror på de alternativ som du anger i kommandot. Här är exempel på utdata från Azure CLI:

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
  .
  .
  .
}
```

Som du ser beskriver egenskaperna konfigurationen av Virtuellt datorn, inte konfigurationen av skaluppsättningen som helhet. Exempel: scale set-modellen har `overprovision` som en egenskap modellen för en virtuell dator i en skala som inte. Orsaken till det här skillnaden är att överetablering är en egenskap för skaluppsättningen som en hela, inte enskilda virtuella datorer i skaluppsättning. (Mer information om överetablering finns [utformning för skalningsuppsättningar](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning).)



### <a name="scale-set-vm-instance-view"></a>Skaluppsättning för VM-instansvyn

Liknande hur en instansvyn har i en skaluppsättning för varje virtuell dator i skaluppsättning för har sin egen Instansvy. Du kan använda om du vill fråga instansvyn för en skaluppsättning för:

* REST API: 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/instanceView?api-version={apiVersion}` 
 
  Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/getinstanceview).

* PowerShell: 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -InstanceView` 
  
  Mer information finns i [PowerShell dokumentationen](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm).

* Azure CLI: 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  Mer information finns i [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view).

Du kan också använda [resursutforskaren i Azure (förhandsversion)](https://resources.azure.com) eller [Azure SDK](https://azure.microsoft.com/downloads/) fråga instansvyn för en virtuell dator i en skaluppsättning.

Hur utdata beror på de alternativ som du anger i kommandot. Här är exempel på utdata från Azure CLI:

```
$ az vmss get-instance-view -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}
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
  .
  .
  .
}
```

Som du kan se beskrivs dessa egenskaper det aktuella runtime-tillståndet för Virtuellt datorn. Tillståndet innehåller alla tillägg som används för skaluppsättningen (utelämnat planeringsaspekter).




## <a name="techniques-for-updating-global-scale-set-properties"></a>Metoder för att uppdatera global skala ange egenskaper

Om du vill uppdatera en global skala den inställda egenskapen, måste du uppdatera egenskapen i scale set-modellen. Du kan göra den här uppdateringen via:

* REST API: 

  `PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
  
  Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

  Du kan också distribuera en Azure Resource Manager-mall med hjälp av egenskaper från REST-API: för att uppdatera egenskaper för global skala.

* PowerShell: 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -VirtualMachineScaleSet {scaleSetConfigPowershellObject}` 
  
  Mer information finns i [PowerShell dokumentationen](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss).

* Azure CLI:

  * Ändra en egenskap: `az vmss update --set {propertyPath}={value}` 
  
  * Lägga till ett objekt i en lista över egenskap i en skaluppsättning: `az vmss update --add {propertyPath} {JSONObjectToAdd}` 
  
  * Ta bort ett objekt från en lista över egenskap i en skaluppsättning: `az vmss update --remove {propertyPath} {indexToRemove}` 
  
  Mer information finns i [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update). 
  
  Alternativt, om du tidigare har distribuerat skaluppsättningen med hjälp av den `az vmss create` kommandot kan du köra den `az vmss create` kommandot igen för att uppdatera skaluppsättning. Om du vill göra detta måste du kontrollera att alla egenskaper i den `az vmss create` kommando är samma som tidigare förutom de egenskaper som du vill ändra.



Du kan också använda [resursutforskaren i Azure (förhandsversion)](https://resources.azure.com) eller [Azure SDK](https://azure.microsoft.com/downloads/) att uppdatera skalan ange modell.

När scale set-modellen har uppdaterats, gäller den nya konfigurationen för alla nya virtuella datorer skapas i skaluppsättning. Dock måste modeller för befintliga virtuella datorer i skaluppsättning fortfarande tas uppdaterade med den senaste övergripande skala modellen. I modellen för varje virtuell dator, en boolesk egenskap kallas `latestModelApplied` anger om den virtuella datorn är uppdaterade med den senaste övergripande skala modellen. (Ett värde av `true` innebär att den virtuella datorn är uppdaterade med den senaste modellen.)




## <a name="techniques-for-bringing-vms-up-to-date-with-the-latest-scale-set-model"></a>Metoder för att skapa virtuella datorer uppdaterade med den senaste modellen med skalan

Skaluppsättningar har en *uppgradera princip* som avgör hur virtuella datorer förs uppdaterade med den senaste modellen med skalan. De tre lägen för princip för versionsuppgradering är:

- **Automatisk**: I detta läge gäller skaluppsättning ger inga garantier om i vilken ordning för virtuella datorer som ansluts ned. Skaluppsättning kan ta bort alla virtuella datorer samtidigt. 
- **Rullande**: I det här läget samlar uppsättningen skala ut uppdateringen i batchar med ett valfritt paus mellan batchar.
- **Manuell**: I det här läget, när du uppdaterar modellen scale set händer ingenting till befintliga virtuella datorer. Om du vill uppdatera befintliga virtuella datorer, måste du manuellt uppgradera var och en. Du kan göra detta manuell uppgradering via:

  - REST API: 
  
    `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/manualupgrade?api-version={apiVersion}` 
    
    Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/updateinstances).

  - PowerShell: 
  
    `Update-AzureRmVmssInstance -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
    
    Mer information finns i [PowerShell dokumentationen](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance).

  - Azure CLI: 
  
    `az vmss update-instances -g {resourceGroupName} -n {vmScaleSetName} --instance-ids {instanceIds}` 
    
    Mer information finns i [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update_instances).

  Du kan också använda den [Azure SDK](https://azure.microsoft.com/downloads/) manuellt uppgradera en virtuell dator i en skaluppsättning.

>[!NOTE]
> Azure Service Fabric-kluster kan använda automatiska läget endast men uppdateringen hanteras annorlunda. Mer information om Service Fabric-uppdateringar finns i [Service Fabric-dokumentationen](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade).

En typ av ändring av egenskaper för global skala inte följer principen för versionsuppgradering: ändringar på skalan som OS-profil. (Exempel är admin användarnamn och lösenord). De här egenskaperna kan vara ändrade endast i API-versionen 2017-12-01 eller senare. Ändringarna gäller endast för virtuella datorer skapas efter ändringen i skalan ange modell. Du måste återavbilda varje befintlig virtuell dator för att göra befintliga virtuella datorer uppdaterade. Du återavbilda en virtuell dator via:

* REST API: 

  `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` 
  
  Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage).

* PowerShell: 

  `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` 
  
  Mer information finns i [PowerShell dokumentationen](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm).

* Azure CLI: 

  `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` 
  
  Mer information finns i [Azure CLI dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage).

Du kan också använda den [Azure SDK](https://azure.microsoft.com/downloads/) till återavbilda en virtuell dator i en skaluppsättning.




## <a name="properties-with-restrictions-on-modification"></a>Egenskaper med begränsningar för ändringar

### <a name="create-time-properties"></a>Skapa egenskaper

Vissa egenskaper kan anges endast när du först skapar skaluppsättning. Dessa egenskaper innehåller:

- Zoner
- avbildningens referens utgivare
- bild referenserbjudande

### <a name="properties-that-can-be-changed-based-on-the-current-value-only"></a>Egenskaper som kan ändras baserat på det aktuella värdet

Vissa egenskaper kan ändras med undantag, beroende på det aktuella värdet. Dessa egenskaper innehåller:

- `singlePlacementGroup`: Om `singlePlacementGroup` är sant, går det att ändra till false. Men om `singlePlacementGroup` är false, den *kan* ändras till true.
- `subnet`: Undernätet i en skaluppsättning kan ändras så länge som det ursprungliga undernätet och det nya undernätet finns i samma virtuella nätverk.

### <a name="properties-that-require-deallocation-to-change"></a>Egenskaper som kräver flyttningen ändra

Vissa egenskaper kan ändras till vissa värden endast om de virtuella datorerna i skaluppsättning har frigjorts. Dessa egenskaper innehåller:

- `sku name`: Om den nya VM SKU inte stöds på den maskinvara som skaluppsättning finns i, måste du frigöra de virtuella datorerna i skaluppsättningen innan du ändrar `sku name`. Mer information om storleksändring av virtuella datorer finns [det här blogginlägget från Azure](https://azure.microsoft.com/blog/resize-virtual-machines/).


## <a name="vm-specific-updates"></a>VM-specifika uppdateringar

Vissa ändringar kan tillämpas på specifika virtuella datorer i stället för global skala ange egenskaper. För närvarande den enda VM-specifik uppdatering som stöds koppla/frånkoppla datadiskar till/från virtuella datorer i skaluppsättning. Den här funktionen är i förhandsgranskningen. Mer information finns i [Förhandsgranska dokumentationen](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).

## <a name="scenarios"></a>Scenarier

### <a name="application-updates"></a>Programuppdateringar

Om ett program distribueras till en skala som anges via tillägg, gör uppdaterar konfigurationen av tillägget programmet som ska uppdateras i enlighet med principen för versionsuppgradering. Till exempel om du har en ny version av ett skript köras i tillägget för anpassat skript du kan uppdatera den `fileUris` egenskapen att peka mot det nya skriptet. 

I vissa fall kanske du vill framtvinga en uppdatering trots att tilläggets konfiguration är oförändrat. (Till exempel du uppdatera skriptet utan att ändra URI för skriptet.) I dessa fall kan du ändra `forceUpdateTag` tvinga fram en uppdatering. Azure-plattformen tolka inte den här egenskapen så att ändra värdet har ingen effekt på hur tillägget körs. Ändra den helt enkelt tvingar tillägget igen. 

Mer information om `forceUpdateTag`, finns det [REST API-dokumentation för tilläggen](https://docs.microsoft.com/rest/api/compute/virtualmachineextensions/createorupdate).

Det är vanligt för program som ska distribueras via en anpassad avbildning. Det här scenariot beskrivs i följande avsnitt.

### <a name="os-updates"></a>OS-uppdateringar

Om du använder plattform bilder, kan du uppdatera avbildningar genom att ändra `imageReference`. Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate).

>[!NOTE]
> Med plattformen bilder är det vanligt att ange ”senaste” för avbildning Referensversion. Det innebär att när skaluppsättningar skapas skaländras ut och avbildade, virtuella datorer skapas med den senaste tillgängliga versionen. Men den *inte* innebär att OS-avbildningen uppdateras automatiskt med tiden som släpps nya avbildningsversioner. Detta är en separat funktion för närvarande under förhandsgranskning. Mer information finns i [automatisk OS uppgraderar](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

Om du använder anpassade avbildningar kan du uppdatera avbildningar genom att uppdatera den `imageReference` -ID. Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="examples"></a>Exempel

### <a name="update-the-os-image-for-your-scale-set"></a>Uppdatera operativsystemsavbildning för din skaluppsättning

Anta att du har en skaluppsättningen körs en gammal version av Ubuntu LTS 16.04. Du vill uppdatera till en nyare version av Ubuntu LTS 16.04 (till exempel version 16.04.201801090). Version för avbildningen referensegenskapen ingår inte i en lista så att du kan ändra dessa egenskaper direkt med hjälp av dessa kommandon:

* PowerShell: 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -ImageReferenceVersion 16.04.201801090`

* Azure CLI: 

  `az vmss update -g {resourceGroupName} -n {vmScaleSetName} --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090`


### <a name="update-the-load-balancer-for-your-scale-set"></a>Uppdatera belastningsutjämnaren för din skaluppsättning

Anta att du har en skala som anges med en Azure belastningsutjämnare och du vill ersätta belastningsutjämnaren med en gateway för Azure-program. Läs in belastningsutjämnare och program gatewayegenskaperna för en skalningsuppsättning är en del av en lista. Därför kan du använda kommandon för att ta bort och lägga till element i stället för att ändra egenskaperna direkt.

PowerShell:
```
# Get the current model of the scale set and store it in a local PowerShell object named $vmss
> $vmss=Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName}

# Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
> $ipconf = New-AzureRmVmssIPConfig myNic -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name

# Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure load balancer) with the new IP configuration
> $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf

# Update the model of the scale set with the new configuration in the local PowerShell object
> Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName} -virtualMachineScaleSet $vmss

```

Azure CLI:
```
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0 # Remove the load balancer back-end pool from the scale set model
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0 # Remove the load balancer back-end pool from the scale set model; only necessary if you have NAT pools configured on the scale set
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}' # Add the application gateway back-end pool to the scale set model
```

>[!NOTE]
> Kommandona förutsätter att det finns endast en IP-konfiguration och Läs in belastningsutjämnaren på skaluppsättning. Om det finns flera, kan du behöva använda en ListIndex än 0.