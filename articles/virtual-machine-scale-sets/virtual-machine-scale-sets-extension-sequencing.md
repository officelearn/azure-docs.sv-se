---
title: Använd tilläggssekvenserning med Azure-skalningsuppsättningar för virtuella datorer
description: Lär dig hur du ordnar tilläggsetablering när du distribuerar flera tillägg på skalningsuppsättningar för virtuella datorer.
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: mimckitt
ms.openlocfilehash: 737040699dd62d722b9a9ad4d8915ccb270c2d06
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273757"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Etablera sekvenstillägg i skalningsuppsättningar för virtuella datorer
Azure-tillägg för virtuella datorer ger funktioner som konfiguration och hantering efter distributionen, övervakning, säkerhet med mera. Produktionsdistributioner använder vanligtvis en kombination av flera tillägg som konfigurerats för VM-instanserna för att uppnå önskat resultat.

När du använder flera tillägg på en virtuell dator är det viktigt att se till att tillägg som kräver samma OS-resurser inte försöker förvärva dessa resurser samtidigt. Vissa tillägg är också beroende av andra tillägg för att tillhandahålla nödvändiga konfigurationer som miljöinställningar och hemligheter. Utan rätt ordning och ordningsföljd på plats kan beroende tilläggsdistributioner misslyckas.

I den här artikeln beskrivs hur du kan ordna tillägg som ska konfigureras för VM-instanser i skalningsuppsättningar för virtuella datorer.

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du är bekant med:
-   [Azure-tillägg](../virtual-machines/extensions/overview.md) för virtuella datorer
-   [Ändra](virtual-machine-scale-sets-upgrade-scale-set.md) skalningsuppsättningar för virtuella datorer

## <a name="when-to-use-extension-sequencing"></a>När ska förlängningssekvensering användas
Sekvenseringstillägg i inte obligatoriska för skalningsuppsättningar, och om inget annat anges kan tillägg etableras på en skalningsuppsättningsinstans i valfri ordning.

Om skalningsuppsättningsmodellen till exempel har två tillägg – ExtensionA och ExtensionB – som anges i modellen, kan någon av följande etableringssekvenser inträffa:
-   ExtensionA -> ExtensionB
-   ExtensionB -> ExtensionA

Om ditt program kräver att tillägg A alltid etableras före tillägg B bör du använda tilläggssekvensering enligt beskrivningen i den här artikeln. Med förlängningssekvensering kommer nu endast en sekvens att inträffa:
-   ExtensionA - > ExtensionB

Alla tillägg som inte anges i en definierad etableringssekvens kan etableras när som helst, inklusive före, efter eller under en definierad sekvens. Förlängningssekvensering anger bara att ett visst tillägg ska etableras efter ett annat specifikt tillägg. Det påverkar inte etableringen av något annat tillägg som definieras i modellen.

Om skalningsuppsättningsmodellen till exempel har tre tillägg – Tillägg A, Tillägg B och Tillägg C – som anges i modellen, och tillägg C är inställt på att etableras efter tillägg A, kan någon av följande etableringssekvenser inträffa:
-   ExtensionA -> ExtensionC -> ExtensionB
-   ExtensionB -> ExtensionA -> ExtensionC
-   ExtensionA -> ExtensionB -> ExtensionC

Om du behöver se till att inget annat tillägg etableras medan den definierade tilläggssekvensen körs rekommenderar vi att alla tillägg i skalningsuppsättningsmodellen sekvenseras. I exemplet ovan kan tillägg B ställas in så att det etableras efter tillägg C så att endast en sekvens kan inträffa:
-   ExtensionA -> ExtensionC -> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>Så här använder du förlängningssekvensering
Om du vill ordna tilläggsetablering måste du uppdatera tilläggsdefinitionen i skalningsuppsättningsmodellen så att egenskapen "provisionAfterExtensions", som accepterar en matris med tilläggsnamn. De tillägg som nämns i egenskapsmatrisvärdet måste vara helt definierade i skaluppsättningsmodellen.

### <a name="template-deployment"></a>Distribution av mall
I följande exempel definieras en mall där skalningsuppsättningen har tre tillägg – ExtensionA, ExtensionB och ExtensionC – så att tillägg etableras i ordningen:
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

Eftersom egenskapen "provisionAfterExtensions" accepterar en matris med tilläggsnamn kan ovanstående exempel ändras så att ExtensionC etableras efter ExtensionA och ExtensionB, men ingen beställning krävs mellan ExtensionA och ExtensionB. Följande mall kan användas för att uppnå det här scenariot:

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
I följande exempel läggs ett nytt tillägg till ExtensionC i en skalningsuppsättningsmodell. ExtensionC har beroenden på ExtensionA och ExtensionB, som redan har definierats i skalningsuppsättningsmodellen.

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

Om ExtensionC definierades tidigare i skalningsuppsättningsmodellen och du nu `PATCH` vill lägga till dess beroenden, kan du köra en för att redigera de redan distribuerade tilläggets egenskaper.

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
Ändringar i befintliga skalentuppsättningsinstanser tillämpas vid nästa [uppgradering](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model).

### <a name="azure-powershell"></a>Azure PowerShell
Använd [cmdleten Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) för att lägga till tillägget Programhälsa i skalningsuppsättningsmodelldefinitionen. Förlängningssekvensering kräver användning av Az PowerShell 1.2.0 eller högre.

I följande exempel läggs tillägget `extensionProfile` [Programhälsa](virtual-machine-scale-sets-health-extension.md) till i en skalningsuppsättningsmodell för en Windows-baserad skaluppsättning. Tillägget Programhälsa etableras efter etablering av [anpassat skripttillägg](../virtual-machines/extensions/custom-script-windows.md), som redan har definierats i skalningsuppsättningen.

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
Använd [az vmss-tillägget för](/cli/azure/vmss/extension#az-vmss-extension-set) att lägga till tillägget Programhälsa i skalningsuppsättningsmodelldefinitionen. Förlängningssekvensering kräver användning av Azure CLI 2.0.55 eller högre.

I följande exempel läggs [tillägget Programhälsa](virtual-machine-scale-sets-health-extension.md) till skalningsuppsättningsmodellen för en Windows-baserad skaluppsättning. Tillägget Programhälsa etableras efter etablering av [anpassat skripttillägg](../virtual-machines/extensions/custom-script-windows.md), som redan har definierats i skalningsuppsättningen.

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

### <a name="not-able-to-add-extension-with-dependencies"></a>Kan du inte lägga till tillägg med beroenden?
1. Kontrollera att de tillägg som anges i provisionAfterExtensions definieras i skalningsuppsättningsmodellen.
2. Se till att det inte införs några cirkulära beroenden. Följande sekvens tillåts till exempel inte: ExtensionA -> ExtensionB -> ExtensionC -> ExtensionA
3. Kontrollera att alla tillägg som du behöver beroenden på har egenskapen "inställningar" under tilläggsegenskaper. Om till exempel ExtentionB måste etableras efter ExtensionA måste ExtensionA ha fältet "inställningar" under ExtensionA "egenskaper". Du kan ange en tom egenskap för "inställningar" om tillägget inte ger några nödvändiga inställningar.

### <a name="not-able-to-remove-extensions"></a>Kan du inte ta bort tillägg?
Se till att de tillägg som tas bort inte finns med under provisionEfterExtensions för andra tillägg.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [distribuerar ditt program](virtual-machine-scale-sets-deploy-app.md) på skalningsuppsättningar för virtuella datorer.
