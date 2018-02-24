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
ms.openlocfilehash: 836d56012afa9e5d5bdec35d85c37dd4b0b788ce
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>Ändra en skaluppsättning för virtuell dator
Den här artikeln beskriver hur du ändrar en befintlig skaluppsättning. Detta innefattar hur du ändrar konfigurationen för skalan ange, hur du ändrar konfigurationen för de program som körs på skalan ange, hur du hanterar tillgänglighet med mera.

## <a name="fundamental-concepts"></a>grundläggande begrepp

### <a name="the-scale-set-model"></a>Modellen skaluppsättning

En skalningsuppsättning har en ”scale set modell” som samlar in den *önskade* ange tillståndet för skalan som helhet. Du kan använda för att fråga modellen för en skalningsuppsättning:

REST-API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` (Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/get))

PowerShell: `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (Mer information finns i [Powershell dokumentationen](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss))

CLI: `az vmss show -g {resourceGroupName} -n {vmSaleSetName}` (Mer information finns i [CLI dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show))

Du kan också använda [resources.azure.com](https://resources.azure.com) eller [Azure SDK](https://azure.microsoft.com/downloads/) att fråga modellen för en skaluppsättning.

Hur utdata beror på de alternativ som du anger i kommandot, men här är några exempel på utdata från CLI:

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



### <a name="the-scale-set-instance-view"></a>Instansvy för skaluppsättning

En skala som också har en ”scale set instans Visa” som samlar in aktuellt *runtime* ange tillståndet för skalan som helhet. Du kan använda om du vill fråga instansvyn för en skaluppsättning för:

REST-API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version={apiVersion}` (Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview))

PowerShell: `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceView` (Mer information finns i [Powershell dokumentationen](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss))

CLI: `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName}` (Mer information finns i [CLI dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view))

Du kan också använda [resources.azure.com](https://resources.azure.com) eller [Azure SDK](https://azure.microsoft.com/downloads/) fråga instansvyn för en skaluppsättning.

Hur utdata beror på de alternativ som du anger i kommandot, men här är ett exempel på utdata från CLI:

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

Som du kan se innehåller dessa egenskaper en sammanfattning av det aktuella runtime-tillståndet för de virtuella datorerna i skalan ange, inklusive innehåller statusen för tillägg som tillämpas på den skaluppsättning (utelämnas planeringsaspekter).



### <a name="the-scale-set-vm-model-view"></a>Skalan ange VM modellvy

Liknande hur vyn modellen har i en skaluppsättning för varje virtuell dator i skaluppsättning för den har sin egen modellvy. Du kan använda för att fråga modellvyn för en skalningsuppsättning:

REST-API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}?api-version={apiVersion}` (Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/get))

PowerShell: `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` (Mer information finns i [Powershell dokumentationen](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm))

CLI: `az vmss show -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` (Mer information finns i [CLI dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show))

Du kan också använda [resources.azure.com](https://resources.azure.com) eller [Azure SDK](https://azure.microsoft.com/downloads/) att fråga modellen för en virtuell dator i en skaluppsättning.

Hur utdata beror på de alternativ som du anger i kommandot, men här är några exempel på utdata från CLI:

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

Som du ser beskriver egenskaperna konfigurationen av Virtuellt datorn, inte konfigurationen av skaluppsättningen som helhet. Exempel: scale set-modellen har `overprovision` som en egenskap, men inte i modellen för en virtuell dator i en skaluppsättning. Den här skillnaden är eftersom överetablering är en egenskap för skaluppsättningen som en hela, inte enskilda virtuella datorer i skaluppsättning (Läs mer om överetablering [denna dokumentation](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning)).



### <a name="the-scale-set-vm-instance-view"></a>VM-instansvyn skaluppsättning

Liknande hur en instansvyn har i en skaluppsättning för varje virtuell dator i skaluppsättning för har sin egen Instansvy. Du kan använda om du vill fråga instansvyn för en skaluppsättning för:

REST-API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/instanceView?api-version={apiVersion}` (Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/getinstanceview))

PowerShell: `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -InstanceView` (Mer information finns i [Powershell dokumentationen](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm))

CLI: `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` (Mer information finns i [CLI dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view))

Du kan också använda [resources.azure.com](https://resources.azure.com) eller [Azure SDK](https://azure.microsoft.com/downloads/) fråga instansvyn för en virtuell dator i en skaluppsättning.

Hur utdata beror på de alternativ som du anger i kommandot, men här är några exempel på utdata från CLI:

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

Som du kan se beskrivs dessa egenskaper det aktuella runtime-tillståndet för den Virtuella datorn, inklusive eventuella tillägg som tillämpas på den skaluppsättning (utelämnas planeringsaspekter).




## <a name="how-to-update-global-scale-set-properties"></a>Ange egenskaper för hur du uppdaterar global skala

Om du vill uppdatera en global skala den inställda egenskapen, måste du uppdatera egenskapen i scale set-modellen. Du kan göra den här uppdateringen via:

REST-API: `PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` (Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate))

Resource Manager-mallar: du kan också distribuera en Resource Manager-mall uppdatera egenskaper för global skala med egenskaper från REST API.

PowerShell: `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -VirtualMachineScaleSet {scaleSetConfigPowershellObject}` (Mer information finns i [Powershell dokumentationen](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss))

CLI. Att ändra en egenskap: `az vmss update --set {propertyPath}={value}`. Lägg till ett objekt till en lista i en skaluppsättning: `az vmss update --add {propertyPath} {JSONObjectToAdd}`. Ta bort ett objekt från en lista över egenskap i en skaluppsättning: `az vmss update --remove {propertyPath} {indexToRemove}`. (Mer information finns i [CLI dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update)). Du kan också om du tidigare har distribuerat skalan anges med den `az vmss create` kommandot kan du köra den `az vmss create` kommandot igen för att uppdatera skala. Om du vill göra detta måste du se till att alla egenskaper i den `az vmss create` kommando är samma som tidigare förutom de egenskaper som du vill ändra.



Du kan också använda [resources.azure.com](https://resources.azure.com) eller [Azure SDK](https://azure.microsoft.com/downloads/) att uppdatera skalan ange modell.

När scale set modellen uppdateras, gäller den nya konfigurationen för alla nya virtuella datorer skapas i skaluppsättning. Dock måste modeller för befintliga virtuella datorer i skaluppsättning fortfarande tas uppdaterade med den senaste övergripande skala modellen. I modellen för varje virtuell dator är en boolesk egenskap kallas `latestModelApplied` som anger om den virtuella datorn är uppdaterade med den senaste övergripande skala modellen (`true` innebär att den virtuella datorn har uppdaterats med den senaste modellen).




## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Hur du hanterar virtuella datorer uppdaterade med den senaste modellen med skalan

Skaluppsättningar har en ”princip för versionsuppgradering” som bestämmer hur virtuella datorer förs uppdaterade med den senaste modellen med skalan. De tre lägen för princip för versionsuppgradering är:

- Automatisk: I detta läge gäller skaluppsättning ger inga garantier om i vilken ordning för virtuella datorer sättas. Skaluppsättning kan ta ned alla virtuella datorer på samma gång. 
- Rullande: I detta läge gäller skaluppsättning samlar ut uppdateringen i batchar med ett valfritt paus mellan batchar.
- Manuell: I det här läget, när du uppdaterar modellen scale set händer ingenting till befintliga virtuella datorer. Om du vill uppdatera befintliga virtuella datorer, måste du göra en ”manuell uppgradering” för varje befintlig virtuell dator. Du kan göra detta manuell uppgradering via:

REST-API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/manualupgrade?api-version={apiVersion}` (Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/updateinstances))

PowerShell: `Update-AzureRmVmssInstance -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` (Mer information finns i [Powershell dokumentationen](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance))

CLI: `az vmss update-instances -g {resourceGroupName} -n {vmScaleSetName} --instance-ids {instanceIds}` (Mer information finns i [CLI dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update_instances)).

Du kan också använda den [Azure SDK](https://azure.microsoft.com/downloads/) att göra en manuell uppgradering på en virtuell dator i en skaluppsättning.

>[!NOTE]
> Service Fabric-kluster kan endast använda läget för automatisk, men uppdateringen hanteras annorlunda. Mer information om service fabric-uppdateringar finns [Service Fabric-dokumentationen](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade).

>[!NOTE]
> Det finns en typ av ändring global skala ange egenskaper som inte följer principen för versionsuppgradering. Dessa är ändringar skala ange OS-profil (till exempel användarnamn och lösenord administratör). Ändringarna gäller endast för virtuella datorer skapas efter ändringen i skalan ange modell. Du måste göra ”återställa” varje befintlig virtuell dator för att göra befintliga virtuella datorer uppdaterade. Du kan göra detta avbildningsåterställning via:

REST-API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (Mer information finns i [REST API-dokumentation](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

PowerShell: `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (Mer information finns i [Powershell dokumentationen](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm))

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (Mer information finns i [CLI dokumentationen](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage)).

Du kan också använda den [Azure SDK](https://azure.microsoft.com/downloads/) till återavbilda en virtuell dator i en skaluppsättning.




## <a name="properties-with-restrictions-on-modification"></a>Egenskaper med begränsningar för ändringar

### <a name="create-time-properties"></a>Skapa egenskaper

Vissa egenskaper kan endast anges när du först skapar skalan. Dessa egenskaper innehåller:

- Zoner
- avbildningens referens utgivare
- bild referenserbjudande

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Egenskaper som kan bara ändras baserat på det aktuella värdet

Vissa egenskaper kan ändras med undantag beroende på det aktuella värdet. Dessa egenskaper innehåller:

- singlePlacementGroup: om singlePlacementGroup är true, den kan ändras till false. Men om singlePlacementGroup är false, den **får inte** ändras till true.
- undernät: undernätet i en skaluppsättning kan ändras så länge som det ursprungliga undernätet och det nya undernätet finns i samma virtuella nätverk.

### <a name="properties-that-require-deallocation-to-change"></a>Egenskaper som kräver flyttningen ändra

Vissa egenskaper kan bara ändras till vissa värden om de virtuella datorerna i skaluppsättning har frigjorts. Dessa egenskaper innehåller:

- SKU-name: om den nya VM-SKU inte stöds på maskinvara skalan är för tillfället, måste du frigöra de virtuella datorerna i skaluppsättningen innan du ändrar sku-namnet. Mer information om storleksändring av virtuella datorer finns [det här blogginlägget från Azure](https://azure.microsoft.com/blog/resize-virtual-machines/).


## <a name="vm-specific-updates"></a>VM-specifika uppdateringar

Vissa ändringar kan tillämpas på specifika virtuella datorer i stället för global skala ange egenskaper. För närvarande den enda VM-specifik uppdatering som stöds koppla/frånkoppla datadiskar till/från virtuella datorer i skaluppsättning. Den här funktionen är i förhandsgranskningen. Mer information finns i [Förhandsgranska dokumentationen](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).

## <a name="scenarios-application-updates-os-updates-etc"></a>Scenarier: Programuppdateringar, OS-uppdateringar och så vidare.

### <a name="application-updates"></a>Programuppdateringar

Om ett program distribueras till en skala som anges via tillägg, gör uppdaterar konfigurationen av tillägget att programmet ska uppdateras i enlighet med principen för versionsuppgradering. Exempelvis kan du uppdatera egenskapen fileUris att peka mot det nya skriptet om du har en ny version av ett skript köras i tillägget för anpassat skript. I vissa fall kan dock kanske du vill framtvinga en uppdatering även om konfigurationen av tillägget är oförändrad (till exempel du uppdaterade skriptet utan att ändra URI för skriptet). I dessa fall kan ändra du forceUpdateTag om du vill framtvinga en uppdatering. Azure-plattformen tolka inte den här egenskapen så att ändra värdet har ingen effekt på hur tillägget körs. Ändra den helt enkelt tvingar tillägget igen. Mer information om forceUpdateTag finns i [REST API-dokumentation för tilläggen](https://docs.microsoft.com/rest/api/compute/virtualmachineextensions/createorupdate).

Det är vanligt för program som ska distribueras via en anpassad avbildning. Det här scenariot beskrivs i avsnittet ”operativsystemuppdateringar”

### <a name="os-updates"></a>OS-uppdateringar

Om du använder plattform bilder, kan du uppdatera bilden genom att ändra imageReference (Mer information finns i den [REST API-dokumentation](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> Med plattformen bilder är det vanligt att ange ”senaste” för avbildning Referensversion. Det innebär att uppsättningen skapar under skala, skala ut och avbildningsåterställning kan skapa de virtuella datorerna med den senaste tillgängliga versionen. Men den **inte** innebär att OS-avbildningen uppdateras automatiskt med tiden som släpps nya avbildningsversioner. Detta är en separat funktion för närvarande under förhandsgranskning. Mer information finns i [automatiska uppgraderingar av OS-dokumentationen](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

Om du använder anpassade avbildningar, kan du uppdatera bilden genom att uppdatera imageReference-ID (Mer information finns i den [REST API-dokumentation](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Exempel

### <a name="updating-the-os-image-for-your-scale-set"></a>Uppdatera operativsystemsavbildning för din skaluppsättning

Anta att du har en skaluppsättningen körs en gammal version av Ubuntu LTS 16.04 och du vill uppdatera till en nyare version av Ubuntu LTS 16.04 (till exempel version 16.04.201801090). Bild referens versionsegenskapen är inte en del av en lista, så att du kan ändra dessa egenskaper med dessa kommandon direkt:

PowerShell: `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -ImageReferenceVersion 16.04.201801090`

CLI: `az vmss update -g {resourceGroupName} -n {vmScaleSetName} --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090`


### <a name="updating-the-load-balancer-for-your-scale-set"></a>Uppdaterar belastningsutjämnaren för din skaluppsättning

Anta att du har en skala som anges med en Azure belastningsutjämnare och du vill ersätta belastningsutjämnaren Azure med en Azure Application Gateway. Belastningen belastningsutjämnare och program gatewayegenskaperna för en skaluppsättning är en del av en lista, så att du kan använda kommandon för att ta bort och lägga till element i stället för att ändra egenskaperna direkt:

PowerShell: 
```
# get the current model of the scale set and store it in a local powershell object named $vmss
> $vmss=Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName}

# create a local powershell object for the new desired IP configuration, which includes the referencerence to the application gateway
> $ipconf = New-AzureRmVmssIPConfig myNic -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name

# replace the existing IP configuration in the local powershell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
> $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf

# Update the model of the scale set with the new configuration in the local powershell object
> Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName} -virtualMachineScaleSet $vmss

```

CLI:
```
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0 # remove the load balancer backend pool from the scale set model
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0 # remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}' # add the application gateway backend pool to the scale set model
```

>[!NOTE]
> Kommandona förutsätter att det finns endast en IP-konfiguration och Läs in belastningsutjämnaren på skaluppsättning. Om det finns flera, kan du behöva använda en ListIndex än 0.