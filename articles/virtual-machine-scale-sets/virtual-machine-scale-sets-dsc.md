---
title: Använda önskad tillstånds konfiguration med Virtual Machine Scale Sets
description: Använd Virtual Machine Scale Sets med Azure Desired State Configuration-tillägget för att konfigurera virtuella datorer.
services: virtual-machine-scale-sets
documentationcenter: ''
author: zjalexander
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
keywords: ''
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 04/05/2017
ms.author: zachal
ms.openlocfilehash: d3d064bc1d9c0a72b10ca27515d8325e7ca7fef9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75359306"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Använda Virtual Machine Scale Sets med Azure DSC-tillägget
[Virtual Machine Scale Sets](virtual-machine-scale-sets-overview.md) kan användas med tilläggs hanteraren för [Azure Desired State Configuration (DSC)](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Skalnings uppsättningar för virtuella datorer ger ett sätt att distribuera och hantera ett stort antal virtuella datorer och kan skalas elastiskt och ut i svar på belastningen. DSC används för att konfigurera de virtuella datorerna när de är online så att de kör produktions program varan.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Skillnader mellan att distribuera till Virtual Machines och Virtual Machine Scale Sets
Den underliggande mallstrukturlistan för en skalnings uppsättning för en virtuell dator skiljer sig något från en enda virtuell dator. Mer specifikt distribuerar en enskild virtuell dator tillägg under noden "virtualMachines". Det finns en post av typen "Extensions" där DSC läggs till i mallen

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

En nod för skalnings uppsättning för virtuell dator har avsnittet "egenskaper" med attributet "VirtualMachineProfile", "extensionProfile". DSC har lagts till under "tillägg"

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

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Beteende för en skalnings uppsättning för virtuell dator
Beteendet för en skalnings uppsättning för virtuella datorer är identiskt med beteendet för en enskild virtuell dator. När en ny virtuell dator skapas, tillhandahålls den automatiskt med DSC-tillägget. Om en nyare version av WMF krävs av tillägget måste den virtuella datorn startas om innan den kan anslutas. När den är online hämtas DSC-konfigurationen. zip och etablera den på den virtuella datorn. Mer information finns i [Översikt över Azure DSC-tillägget](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Nästa steg
Granska [Azure Resource Manager-mallen för DSC-tillägget](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Lär dig hur [DSC-tillägget hanterar autentiseringsuppgifter på ett säkert sätt](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Mer information om tilläggs hanteraren för Azure DSC finns i [Introduktion till tillägget Azure Desired State Configuration](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Mer information om PowerShell DSC [finns i PowerShell-dokumentations centret](/powershell/scripting/dsc/overview/overview). 

