---
title: Använda tillägg finns med Azure VM scale sets | Microsoft Docs
description: Lär dig hur du sekvenserar tillägget etablering när du distribuerar flera tillägg på VM-skalningsuppsättningar.
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
ms.openlocfilehash: 2e5dfda16c4828b3113fc50d4cffc79fe6ff19e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620180"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Anger sekvens tillägget etablering i VM-skalningsuppsättning
Azure VM-tillägg ger funktioner, till exempel konfigurationen efter distribution och hantering, övervakning, säkerhet och mer. Produktionsdistribution använder vanligtvis en kombination av flera tillägg som konfigurerats för VM-instanser för att uppnå önskat resultat.

När du använder flera tillägg på en virtuell dator, är det viktigt att se till att tillägg som kräver samma OS-resurser inte försöker hämta dessa resurser på samma gång. Vissa tillägg beror också på andra tillägg att tillhandahålla nödvändiga konfigurationer, till exempel miljöinställningar och hemligheter. Utan rätt ordning och sekvensering på plats, kan beroende tillägget distributioner misslyckas.

Den här artikeln beskriver hur du kan ordna tillägg som ska konfigureras för VM-instanser i skalningsuppsättningar för virtuella datorer.

## <a name="prerequisites"></a>Nödvändiga komponenter
Den här artikeln förutsätter att du är bekant med:
-   Azure-dator [tillägg](../virtual-machines/extensions/overview.md)
-   [Ändra](virtual-machine-scale-sets-upgrade-scale-set.md) VM-skalningsuppsättningar

## <a name="when-to-use-extension-sequencing"></a>När du ska använda ordningsföljd
Ordningsföljd för tillägg i inte obligatorisk för att skala anger och om inget anges, tillägg kan etableras på en skala set-instans i valfri ordning.

Till exempel om din skalningsuppsättningen har två tillägg – ExtensionA och ExtensionB – anges i modellen, uppstå sedan något av följande etablering sekvenser:
-   ExtensionA -> ExtensionB
-   ExtensionB -> ExtensionA

Om programmet kräver ett tillägg som alltid ska etableras innan tillägg B, bör du använda ordningsföljd som beskrivs i den här artikeln. Med tillägg finns inträffar bara en sekvens nu:
-   ExtensionA - > ExtensionB

Några tillägg som inte har angetts i en sekvens med definierade etablering kan etableras när som helst, inklusive före, efter eller under en definierad sekvens. Ordningsföljd för enbart anger att ett visst tillägg kommer att tillhandahållas när du har en annan specifika tillägget. Det påverkar inte etableringen av alla andra tillägg som definierats i modellen.

Exempel: om din skalningsuppsättningen har tre tillägg – tillägget A, tillägg B och tillägg C – anges i modellen, och tillägg C har angetts som ska etableras när tillägget A, sedan något av följande etablering sekvenser förekomma:
-   ExtensionA -> ExtensionC -> ExtensionB
-   ExtensionB -> ExtensionA -> ExtensionC
-   ExtensionA -> ExtensionB -> ExtensionC

Om du behöver säkerställa att inga andra tillägg etableras medan de definierade tillägget körs, rekommenderar vi ordningsföljd för alla tillägg i din skalningsuppsättningen. I exemplet ovan kan du ange tillägg B som ska etableras efter tillägg C, så att endast en sekvens kan inträffa:
-   ExtensionA -> ExtensionC -> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>Hur du använder tillägg finns
Att sekvens tillägget etablering, måste du uppdatera tillägget definitionen i skalningsuppsättningen att egenskapen ”provisionAfterExtensions”, som tar emot en matris med datatilläggsnamnen. De tillägg som nämns i matrisen egenskapsvärdet måste fullständigt definieras i modellen i skalningsuppsättningen.

### <a name="template-deployment"></a>Malldistribution
I följande exempel definierar en mall där skalningsuppsättningen har tre tillägg – ExtensionA, ExtensionB och ExtensionC – så att tillägg levereras i ordning:
-   ExtensionA -> ExtensionB -> ExtensionC

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA"
          ],
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

Eftersom egenskapen ”provisionAfterExtensions” tar emot en matris med Tilläggsnamn, kan exemplet ovan ändras så att ExtensionC har etablerats efter ExtensionA och ExtensionB, men ingen ordning krävs mellan ExtensionA och ExtensionB. Följande mall kan användas för att uppnå det här scenariot:

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA","ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

### <a name="rest-api"></a>REST-API
I följande exempel lägger till ett nytt tillägg med namnet ExtensionC i en skalningsuppsättningsmodell. ExtensionC har beroenden på ExtensionA och ExtensionB, vilka har redan definierats i modellen i skalningsuppsättningen.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "name": "ExtensionC",
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ],
    "publisher": "ExtensionC.Publisher",
    "settings": {},
    "typeHandlerVersion": "3.0",
    "autoUpgradeMinorVersion": true,
    "type": "ExtensionC" 
  }                  
}
```

Om ExtensionC definierades tidigare i skaluppsättningen modell och du nu vill du lägga till dess beroenden, kan du köra en `PATCH` att redigera egenskaperna för redan distribuerade tillägget.

```
PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ]
  }                  
}
```
Ändringar i befintliga skalningsuppsättningsinstanser tillämpas på nästa [uppgradera](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-powershell"></a>Azure PowerShell
Använd den [Lägg till AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet för att lägga till tillägget programmets hälsotillstånd till skalan modellen principuppsättningsdefinition. Ordningsföljd för kräver användning av Az PowerShell 1.2.0 eller senare.

I följande exempel läggs den [Programhälsa tillägget](virtual-machine-scale-sets-health-extension.md) till den `extensionProfile` i en skalningsuppsättning skalningsuppsättningsmodellen för en Windows-baserad skalningsuppsättning. Tillägget programmets hälsotillstånd kommer att tillhandahållas när du har etablerat den [tillägget för anpassat skript](../virtual-machines/extensions/custom-script-windows.md)har redan definierats i skalningsuppsättningen.

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
  -ProvisionAfterExtension "CustomScriptExtension" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Använd [az vmss-tilläggsuppsättningen](/cli/azure/vmss/extension#az-vmss-extension-set) att lägga till tillägget programmets hälsotillstånd till skalningsuppsättningen Service-definitionen. Ordningsföljd för kräver användning av Azure CLI 2.0.55 eller senare.

I följande exempel läggs den [Programhälsa tillägget](virtual-machine-scale-sets-health-extension.md) till skalan skalningsuppsättningsmodellen för en Windows-baserad skalningsuppsättning. Tillägget programmets hälsotillstånd kommer att tillhandahållas när du har etablerat den [tillägget för anpassat skript](../virtual-machines/extensions/custom-script-windows.md)har redan definierats i skalningsuppsättningen.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --provision-after-extensions CustomScriptExtension \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Felsöka

### <a name="not-able-to-add-extension-with-dependencies"></a>Det går inte att lägga till tillägget med beroenden?
1. Se till att de tillägg som anges i provisionAfterExtensions definieras i modellen i skalningsuppsättningen.
2. Se till att det finns inga cirkulärt tjänstberoende som introduceras. Till exempel är inte följande sekvens tillåten: ExtensionA -> ExtensionB -> ExtensionC -> ExtensionA
3. Kontrollera att alla tillägg du vidta beroenden, har en ”inställningar”-egenskap under tillägget ”egenskaper”. Till exempel om ExtentionB behöver etableras efter ExtensionA, ha sedan ExtensionA fältet ”inställningar” under ExtensionA ”egenskaper”. Du kan ange en tom ”inställningar”-egenskap om filnamnstillägget inte tvingade inställningar som krävs.

### <a name="not-able-to-remove-extensions"></a>Det går inte att ta bort tillägg?
Se till att tilläggen som tas bort inte visas under provisionAfterExtensions för alla andra tillägg.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [distribuera ditt program](virtual-machine-scale-sets-deploy-app.md) på virtuella datorer du anger.
