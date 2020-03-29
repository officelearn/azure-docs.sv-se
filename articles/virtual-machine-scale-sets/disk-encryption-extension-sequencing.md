---
title: Azure Disk Encryption och Azure virtual machine scale anger tilläggssekvensering
description: Den här artikeln innehåller instruktioner om hur du aktiverar Microsoft Azure Disk Encryption för virtuella Linux IaAS-datorer.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.openlocfilehash: aa638b86b0788b8c274f9dcb3c04c1fc385b4ae1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279019"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Använda Azure Disk Encryption med sekvensering av tillägg för virtuell datoruppsättning

Tillägg som Azure-diskkryptering kan läggas till i en Azure-skala för virtuella datorer som anges i en angiven ordning. Det gör du genom att använda [förlängningssekvensering](virtual-machine-scale-sets-extension-sequencing.md). 

I allmänhet bör kryptering tillämpas på en disk:

- Efter tillägg eller anpassade skript som förbereder diskarna eller volymerna.
- Före tillägg eller anpassade skript som kommer åt eller använder data på de krypterade diskarna eller volymerna.

I båda fallen `provisionAfterExtensions` anger egenskapen vilket tillägg som ska läggas till senare i sekvensen.

## <a name="sample-azure-templates"></a>Exempel på Azure-mallar

Om du vill att Azure Disk Encryption ska `provisionAfterExtensions` tillämpas efter ett annat tillägg placerar du egenskapen i AzureDiskEncryption-tilläggets block. 

Här är ett exempel med "CustomScriptExtension", ett Powershell-skript som initierar och formaterar en Windows-disk, följt av "AzureDiskEncryption":

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

Om du vill att Azure Disk Encryption ska `provisionAfterExtensions` tillämpas före ett annat tillägg, placera egenskapen i blocket av tillägget som ska följas.

Här är ett exempel med "AzureDiskEncryption" följt av "VMDiagnosticsSettings", ett tillägg som tillhandahåller övervaknings- och diagnostikfunktioner på en Windows-baserad Virtuell Azure:Here is a example using "AzureDiskTEncryption" followed by "VMDiagnosticsSettings", ett tillägg som tillhandahåller övervaknings- och diagnostikfunktioner på en Windows-baserad Azure VM:


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

En mer djupgående mall finns i:
* Använda Azure Disk Encryption-tillägget efter ett anpassat skalskript som formaterar disken (Linux): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>Nästa steg
- Läs mer om om ordningsföljd för tillägg: [Sekvenstilläggetablering i skalningsuppsättningar för virtuella datorer](virtual-machine-scale-sets-extension-sequencing.md).
- Läs mer `provisionAfterExtensions` om egenskapen: [Microsoft.Compute virtualMachineScaleSets/extensions mallreferens](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
- [Azure Disk Encryption för skalningsuppsättningar för virtuella datorer](disk-encryption-overview.md)
- [Kryptera en skala för virtuella datorer med Azure CLI](disk-encryption-cli.md)
- [Kryptera en skala för virtuella datorer med Azure PowerShell](disk-encryption-powershell.md)
- [Skapa och konfigurera ett nyckelvalv för Azure DiskKryptering](disk-encryption-key-vault.md)
