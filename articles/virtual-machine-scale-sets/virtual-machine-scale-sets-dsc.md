---
title: Använda önskad tillståndskonfiguration med skaluppsättningar för virtuella datorer
description: Använda skalningsuppsättningar för virtuella datorer med Azure Desired State Configuration Extension för att konfigurera virtuella datorer.
author: zjalexander
tags: azure-service-management,azure-resource-manager
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.date: 04/05/2017
ms.author: zachal
ms.openlocfilehash: a93a8a9c27be5a1736a50e6c4c4b830980b7d974
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278074"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Använda skalningsuppsättningar för virtuella datorer med Azure DSC-tillägget
[Skala uppsättningar för virtuella datorer](virtual-machine-scale-sets-overview.md) kan användas med [DSC-tilläggshanteraren (Azure Desired State Configuration).](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Skalningsuppsättningar för virtuella datorer är ett sätt att distribuera och hantera ett stort antal virtuella datorer och kan elastiskt skala in och ut som svar på belastning. DSC används för att konfigurera de virtuella datorerna när de är online så att de kör produktionsprogrammet.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Skillnader mellan distribution till virtuella datorer och skaluppsättningar för virtuella datorer
Den underliggande mallstrukturen för en skaluppsättning för virtuella datorer skiljer sig något från en enda virtuell dator. Närmare bestämt distribuerar en enda virtuell dator tillägg under noden "virtualMachines". Det finns en post av typen "tillägg" där DSC läggs till i mallen

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

En nod för skalningsuppsättning för virtuell dator har avsnittet "egenskaper" med attributet "VirtualMachineProfile", "extensionProfile". DSC läggs till under "tillägg"

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Beteende för en skaluppsättning för virtuella datorer
Beteendet för en skalningsuppsättning för virtuella datorer är identiskt med beteendet för en enda virtuell dator. När en ny virtuell dator skapas etableras den automatiskt med DSC-tillägget. Om en nyare version av WMF krävs av tillägget startas den virtuella datorn om innan den kommer online. När den är online hämtas DSC-konfigurationen .zip och etablera den på den virtuella datorn. Mer information finns i [Översikt över Azure DSC-tillägg](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Nästa steg
Undersök [Azure Resource Manager-mallen för DSC-tillägget](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Lär dig hur [DSC-tillägget hanterar autentiseringsuppgifterna på ett säkert sätt](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Mer information om Azure DSC-tilläggshanteraren finns i [Introduktion till azure desired state configuration extension-hanteraren](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Mer information om PowerShell DSC [finns i PowerShells dokumentationscenter](/powershell/scripting/dsc/overview/overview). 

