---
title: Azure Monitor beroende virtuell dators tillägg för Linux | Microsoft Docs
description: Distribuera den Azure Monitor beroende agenten på en virtuell Linux-dator med hjälp av ett tillägg för virtuell dator.
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 416b0c89105f97514efdfcc859a630d78f7ba7f5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084838"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Azure Monitor beroende virtuell dators tillägg för Linux

I Azure Monitor för virtuella datorer kartan funktionen hämtar data från Microsoft Dependency agenten. Tillägget virtuell dator i Azure VM-beroende agent för Linux publiceras och stöds av Microsoft. Tillägget installerar beroende agenten på virtuella Azure-datorer. Det här dokumentet innehåller information om de plattformar, konfigurationer och distributions alternativ som stöds för Azure VM-beroende agent för virtuell dator tillägg för Linux.

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

Tillägget Azure VM Dependency agent för Linux kan köras mot de operativ system som stöds i avsnittet [operativ system som stöds](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) i artikeln Azure Monitor for VMS distribution.

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för tillägget Azure VM Dependency agent på en virtuell Azure Linux-dator. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>Egenskapsvärden

| Name | Värde/exempel |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Malldistribution

Du kan distribuera virtuella Azure-tillägg med Azure Resource Manager mallar. Du kan använda JSON-schemat som beskrivs i föregående avsnitt i en Azure Resource Manager mall för att köra tillägget Azure VM-beroende agent under en Azure Resource Manager mall-distribution.

JSON för ett tillägg för virtuell dator kan kapslas i den virtuella dator resursen. Eller så kan du placera den på rot-eller toppnivå i en Resource Manager JSON-mall. Placeringen av JSON påverkar värdet för resurs namn och typ. Mer information finns i [ange namn och typ för underordnade resurser](../../azure-resource-manager/child-resource-name-type.md).

I följande exempel förutsätts att tillägget för beroende agent är kapslat i den virtuella dator resursen. När du kapslar in tilläggs resursen placeras JSON i `"resources": []` objektet på den virtuella datorn.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

När du placerar tillägget JSON i roten för mallen innehåller resurs namnet en referens till den överordnade virtuella datorn. Typen visar den kapslade konfigurationen. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Du kan använda Azure CLI för att distribuera beroende agentens VM-tillägg till en befintlig virtuell dator.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Data om tillstånd för tilläggs distributioner kan hämtas från Azure Portal och med hjälp av Azure CLI. Om du vill se distributions statusen för tillägg för en virtuell dator kör du följande kommando med hjälp av Azure CLI:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Tillägget utförande-utdatan loggas till följande fil:

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).
