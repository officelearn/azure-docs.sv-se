---
title: Med hjälp av Desired State Configuration med Skalningsuppsättningar i virtuella | Microsoft Docs
description: Med virtuella skalan uppsättningar med Azure DSC-tillägg
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
ms.openlocfilehash: a68a5f31952d636c054b66dc0bb6ec0579cd7192
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Med virtuella skalan uppsättningar med Azure DSC-tillägg
[Skaluppsättningar för den virtuella datorn](virtual-machine-scale-sets-overview.md) kan användas med den [Azure önskad tillstånd Configuration (DSC)](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) tillägget hanterare. Skaluppsättningar för den virtuella datorn är ett sätt att distribuera och hantera ett stort antal virtuella datorer och Elastiskt kan skala in eller ut för att läsa in. DSC används för att konfigurera de virtuella datorerna som de är online så att de kör programmet för produktion.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Skillnader mellan distribution till virtuella datorer och virtuella datorer
Den underliggande strukturen i mallen för en skaluppsättning för virtuell dator är skiljer sig från en enda virtuell dator. En enda virtuell dator distribuerar specifikt tillägg under noden ”virtualMachines”. Det finns en post av typen ”tillägg” där DSC har lagts till i mallen

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

En virtuell dator skala set-nod har ett ”egenskaper” avsnitt med den ”VirtualMachineProfile”, ”extensionProfile”-attribut. DSC läggs till under ”tillägg”

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

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Beteende för en Skaluppsättning för virtuell dator
Beteende för en skaluppsättning för virtuell dator är identiskt med beteendet för en enskild virtuell dator. När en ny virtuell dator skapas den automatiskt har etablerats med DSC-tillägg. Om en nyare version av WMF krävs av tillägget för den virtuella datorn startas om innan kommer online. När den är online, hämtar .zip för DSC-konfigurationen och etablera på den virtuella datorn. Mer information finns i [Azure DSC-tillägg översikt](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Nästa steg
Granska de [Azure Resource Manager-mall för DSC-tillägg](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Lär dig hur [DSC-tillägg på ett säkert sätt hanterar autentiseringsuppgifter](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Mer information om Azure DSC-tillägg-hanteraren finns [introduktion till Azure Desired State Configuration-tillägget hanteraren](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Mer information om PowerShell DSC [finns på PowerShell documentation center](https://msdn.microsoft.com/powershell/dsc/overview). 

