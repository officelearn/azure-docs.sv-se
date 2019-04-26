---
title: Med Desired State Configuration med Virtual Machine Scale Sets | Microsoft Docs
description: Virtuell dator använder Skalningsuppsättningar med Azure DSC-tillägget
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
ms.openlocfilehash: 24a37d352413ff9ac55ce8e189691988383950f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60204000"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Virtuell dator använder Skalningsuppsättningar med Azure DSC-tillägget
[Virtual Machine Scale Sets](virtual-machine-scale-sets-overview.md) kan användas med den [Azure Desired State Configuration (DSC)](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) tilläggshanterare. VM-skalningsuppsättningar är ett sätt att distribuera och hantera ett stort antal virtuella datorer och kan Elastiskt skala in och ut i svar att läsa in. DSC används för att konfigurera de virtuella datorerna som de är online så att de kör programvara för produktion.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Skillnaderna mellan att distribuera virtuella datorer och Virtual Machine Scale Sets
Den underliggande strukturen i mallen för en VM-skalningsuppsättning är skiljer sig från en enda virtuell dator. Mer specifikt distribuerar en enskild virtuell dator tillägg under noden ”virtualMachines”. Det finns en post av typen ”tillägg” där DSC läggs till i mallen

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

En VM scale set-nod har ett ”egenskaper”-avsnitt med den ”VirtualMachineProfile”, ”extensionProfile”-attributet. DSC läggs till under ”tillägg”

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

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Beteende för en VM-Skalningsuppsättning
Beteendet för en VM-skalningsuppsättning är identiskt med beteendet för en enskild virtuell dator. När en ny virtuell dator skapas, etableras den automatiskt med DSC-tillägget. Om en nyare version av WMF krävs av tillägget, startar om den virtuella datorn innan du kopplar upp sig online. När den är online, laddar ned .zip för DSC-konfiguration och etablera den på den virtuella datorn. Mer information finns i [Azure DSC-tillägget översikt](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Nästa steg
Granska den [Azure Resource Manager-mall för DSC-tillägget](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Lär dig hur [DSC-tillägget på ett säkert sätt hanterar autentiseringsuppgifter](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Läs mer på Azure DSC-tilläggshanterare [introduktion till Azure Desired State Configuration-tilläggshanterare](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Mer information om PowerShell DSC [besök dokumentationscentret för PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

