---
title: Azure Disk Encryption och Azure VM scale sets ordningsföljd
description: Den här artikeln innehåller anvisningar om hur du aktiverar Microsoft Azure Disk Encryption för virtuella Linux IaaS-datorer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/21/2019
ms.openlocfilehash: e98e501806971f3cf1bec29960ad15ef9c0024fc
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498153"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Använd Azure Disk Encryption med VM-skalningsuppsättning ange ordningsföljd

Tillägg, till exempel Azure-diskkryptering kan läggas till i en skalningsuppsättning för virtuella Azure-datorer i en angiven ordning. Du gör detta genom att använda [ordningsföljd för](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). 

I allmänhet ska kryptering tillämpas på en disk:

- När du har tillägg eller anpassade skript som förbereder diskar och volymer.
- Innan du tillägg eller anpassade skript för att komma åt eller använda data på krypterade diskar och volymer.

I båda fallen den `provisionAfterExtensions` egenskapen anger vilka tillägg ska läggas till senare i sekvensen.

## <a name="sample-azure-templates"></a>Exemplet Azure-mallar

Om du vill att Azure Disk Encryption tillämpas efter ytterligare ett tillägg, placera den `provisionAfterExtensions` -egenskapen i blocket AzureDiskEncryption tillägget. 

Här är ett exempel som använder ”CustomScriptExtension”, ett Powershell-skript som initierar och formaterar en Windows-disk, följt av ”AzureDiskEncryption”:

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

Om du vill att Azure Disk Encryption används innan det ytterligare ett tillägg, placera den `provisionAfterExtensions` -egenskapen i blocket av tillägget för att följa.

Här är ett exempel som använder ”AzureDiskEncryption” följt av ”VMDiagnosticsSettings”, ett tillägg som tillhandahåller övervakning och diagnostik funktioner på en Windows-baserad virtuell Azure-dator:


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

För mer djupgående mallar, se:
* Tillämpa Azure Disk Encryption-tillägget när du har en anpassad kommandoskript som formaterar disken (Linux): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)
* Tillämpa Azure Disk Encryption-tillägget när du har en anpassad Powershell-skript som initierar och formaterar disken (Windows): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-ADE-after-customscript.json)
* Tillämpa Azure Disk Encryption-tillägget innan du en anpassad Powershell-skript som initierar och formaterar disken (Windows): [deploy-extseq-windows-CustomScript-after-ADE.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-CustomScript-after-ADE.json)

## <a name="next-steps"></a>Nästa steg
- Mer information om tillägg finns: [Sekvensera tillägget etablering i VM-skalningsuppsättningar](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md).
- Läs mer om den `provisionAfterExtensions` egenskapen: [Mallreferensen för Microsoft.Compute virtualMachineScaleSets/tillägg](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
