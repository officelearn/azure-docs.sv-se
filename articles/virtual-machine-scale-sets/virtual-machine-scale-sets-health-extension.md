---
title: Använda program hälso tillägg med skalnings uppsättningar för virtuella Azure-datorer | Microsoft Docs
description: Lär dig hur du använder tillägget för program hälsa för att övervaka hälso tillståndet för dina program som distribueras på virtuella datorers skalnings uppsättningar.
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
ms.openlocfilehash: e074d76f9ed095725d99bddc9eb21925f4b3697c
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114471"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Använda program hälso tillägg med skalnings uppsättningar för virtuella datorer
Övervakning av din program hälsa är en viktig signal för att hantera och uppgradera distributionen. Skalnings uppsättningar för virtuella Azure-datorer ger stöd för [rullande uppgraderingar](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , inklusive [automatiska uppgraderingar av operativ system avbildningar](virtual-machine-scale-sets-automatic-upgrade.md), som förlitar sig på hälso övervakning av de enskilda instanserna för att uppgradera distributionen.

I den här artikeln beskrivs hur du kan använda tillägget för program hälsa för att övervaka hälso tillståndet för dina program som distribueras på virtuella datorers skalnings uppsättningar.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du är bekant med:
-   [Tillägg](../virtual-machines/extensions/overview.md) för virtuella Azure-datorer
-   [Ändra](virtual-machine-scale-sets-upgrade-scale-set.md) skalnings uppsättningar för virtuella datorer

## <a name="when-to-use-the-application-health-extension"></a>När du ska använda program hälso tillägget
Program hälso tillägget distribueras i en instans av en skalnings uppsättning för virtuella datorer och rapporter om VM-hälsa inifrån skalnings uppsättnings instansen. Du kan konfigurera tillägget för avsökning på en program slut punkt och uppdatera status för programmet på den instansen. Den här instans statusen kontrol leras av Azure för att avgöra om en instans är tillgänglig för uppgraderings åtgärder.

När tillägget rapporterar hälso tillstånd från en virtuell dator kan tillägget användas i situationer där externa avsökningar, till exempel program hälso avsökningar (som använder anpassade Azure Load Balancer [](../load-balancer/load-balancer-custom-probe-overview.md)avsökningar) inte kan användas.

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för program hälso tillägget. Tillägget kräver minst en "TCP"-eller http-begäran med en associerad port eller begär ande sökväg.

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
| apiVersion | `2018-10-01` | date |
| publisher | `Microsoft.ManagedServices` | sträng |
| type | `ApplicationHealthLinux`(Linux), `ApplicationHealthWindows` (Windows) | sträng |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Inställningar

| Name | Värdet / exempel | Datatyp
| ---- | ---- | ----
| protocol | `http` eller `tcp` | sträng |
| port | Valfritt om protokollet är `http`obligatoriskt när protokollet är`tcp` | int |
| requestPath | Obligatoriskt när protokollet är `http`, tillåts inte när protokollet är`tcp` | sträng |

## <a name="deploy-the-application-health-extension"></a>Distribuera program hälso tillägget
Det finns flera sätt att distribuera program hälso tillägget till dina skalnings uppsättningar enligt beskrivningen i exemplen nedan.

### <a name="rest-api"></a>REST-API

I följande exempel läggs program hälso tillägget (med namnet myHealthExtension) till i extensionProfile i skalnings uppsättnings modellen för en Windows-baserad skalnings uppsättning.

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
Används `PATCH` för att redigera ett redan distribuerat tillägg.

### <a name="azure-powershell"></a>Azure PowerShell

Använd cmdleten [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) för att lägga till program hälso tillägget i modell definitionen för skalnings uppsättningen.

`extensionProfile` I följande exempel läggs program hälso tillägget till i skalnings uppsättnings modellen för en Windows-baserad skalnings uppsättning. Exemplet använder den nya AZ PowerShell-modulen.

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

Använd [AZ VMSS Extension set](/cli/azure/vmss/extension#az-vmss-extension-set) för att lägga till program hälso tillägget i modell definitionen för skalnings uppsättningen.

I följande exempel läggs program hälso tillägget till i skalnings uppsättnings modellen för en Linux-baserad skalnings uppsättning.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
Innehållet i fil namns tillägget. JSON.

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>Felsöka
Utökning av utdata loggas till filer som finns i följande kataloger:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

Loggarna avbildar också program hälso status regelbundet.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [distribuerar ditt program](virtual-machine-scale-sets-deploy-app.md) på virtuella datorers skalnings uppsättningar.
