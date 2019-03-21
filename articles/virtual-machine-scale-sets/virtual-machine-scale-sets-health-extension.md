---
title: Använda programmets hälsotillstånd tillägget med Azure VM scale sets | Microsoft Docs
description: Lär dig hur du använder tillägget programmets hälsotillstånd för att övervaka hälsotillståndet för dina program distribuerade på VM-skalningsuppsättningar.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: manayar
ms.openlocfilehash: d1cff1011e190e5fbb2874657cbdfbdc68bde0c0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58084403"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Med hjälp av programmets hälsotillstånd anger tillägg med VM-skalningsuppsättning
Övervakning av programmets hälsotillstånd är ett viktigt signal för att hantera och uppgradera din distribution. Azure VM-skalningsuppsättningar har stöd för [löpande uppgraderingar](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) inklusive [automatiska uppgraderingar av OS-avbildning](virtual-machine-scale-sets-automatic-upgrade.md), som förlitar sig på hälsoövervakning av enskilda instanser att uppgradera din distribution .

Den här artikeln beskrivs hur du kan använda tillägget programmets hälsotillstånd för att övervaka hälsotillståndet för dina program distribuerade på VM-skalningsuppsättningar.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du är bekant med:
-   Azure-dator [tillägg](../virtual-machines/extensions/overview.md)
-   [Ändra](virtual-machine-scale-sets-upgrade-scale-set.md) VM-skalningsuppsättningar

## <a name="when-to-use-the-application-health-extension"></a>När du ska använda tillägget programmets hälsotillstånd
Programmets hälsotillstånd-tillägget har distribuerats i en VM scale set-instans och rapporter på VM-hälsa i skalningsuppsättningens datorinstans. Du kan konfigurera tillägget för att avsökning på en programslutpunkt och uppdatera status för programmet på den instansen. Den här instansen kontrolleras av Azure för att avgöra om en instans är berättigade till åtgärder för uppgradering.

Som tillägget rapporter hälsotillståndet från en virtuell dator, tillägget kan användas i situationer där externa avsökningar, till exempel programmet Hälsoavsökningar (som använder anpassade Azure Load Balancer [avsökningar](../load-balancer/load-balancer-custom-probe-overview.md)) kan inte användas.

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för tillägget programmets hälsotillstånd. Tillägget kräver minst en ”tcp” eller ”http”-begäran med en tillhörande port eller sökväg för begäran respektive.

```json
{
  "type": "extensions",
  "name": "HealthExtension",
  "apiVersion": "2018-10-01",
  "location": "<location>",  
  "properties": {
    "publisher": "Microsoft.ManagedServices",
    "type": "< ApplicationHealthLinux or ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}  
```

### <a name="property-values"></a>Egenskapsvärden

| Namn | Värdet / exempel | Datatyp
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | datum |
| utgivare | `Microsoft.ManagedServices` | sträng |
| typ | `ApplicationHealthLinux` (Linux), `ApplicationHealthWindows` (Windows) | sträng |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Inställningar

| Namn | Värdet / exempel | Datatyp
| ---- | ---- | ----
| protokoll | `http` eller `tcp` | sträng |
| port | Valfritt när protokollet är `http`obligatorisk när protokollet är `tcp` | int |
| requestPath | Obligatoriskt när protokollet är `http`, inte tillåts när protokollet är `tcp` | sträng |

## <a name="deploy-the-application-health-extension"></a>Distribuera tillägget programmets hälsotillstånd
Det finns flera olika sätt att distribuera Programhälsa tillägg till din skalningsuppsättning anger som beskrivs i exemplen nedan.

### <a name="rest-api"></a>REST-API

I följande exempel lägger till tillägget programmets hälsotillstånd (med namnet myHealthExtension) extensionProfile i skalningsuppsättningen för en Windows-baserad skalningsuppsättning.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/myHealthExtension?api-version=2018-10-01`
```

```json
{
  "name": "myHealthExtension",
  "properties": {
    "publisher": " Microsoft.ManagedServices",
    "type": "< ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}
```
Använd `PATCH` så här redigerar du ett redan distribuerat tillägg.

### <a name="azure-powershell"></a>Azure PowerShell

Använd den [Lägg till AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet för att lägga till tillägget programmets hälsotillstånd till skalan modellen principuppsättningsdefinition.

I följande exempel läggs Programhälsa tillägget till den `extensionProfile` i skalningsuppsättningen skalningsuppsättningsmodellen för en Windows-baserad skalningsuppsättning. I exemplet används den nya Az PowerShell-modulen.

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```


### <a name="azure-cli-20"></a>Azure CLI 2.0

Använd [az vmss-tilläggsuppsättningen](/cli/azure/vmss/extension#az-vmss-extension-set) att lägga till tillägget programmets hälsotillstånd till skalningsuppsättningen Service-definitionen.

I följande exempel lägger till tillägget Programhälsa i skalningsuppsättningsmodell för en Windows-baserad skalningsuppsättning.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Felsöka
Tillägget utförande-utdatan loggas till filer som finns i följande kataloger:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

Loggarna avbilda också med jämna mellanrum hälsotillståndet för programmet.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [distribuera ditt program](virtual-machine-scale-sets-deploy-app.md) på virtuella datorer du anger.
