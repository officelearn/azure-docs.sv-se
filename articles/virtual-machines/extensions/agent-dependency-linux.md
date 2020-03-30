---
title: Azure Monitor Dependency virtual machine-tillägg för Linux
description: Distribuera Azure Monitor Dependency Agent på Linux virtuell dator med hjälp av en virtuell dator tillägg.
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
ms.openlocfilehash: 82f9c5a67cb056752cf8310be3b7c9f0bd2501e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254045"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Azure Monitor Dependency virtual machine-tillägg för Linux

Funktionen Azure Monitor för virtuella datorer kart får sina data från Microsoft Dependency Agent. Virtual Machine-tillägget för azure VM Dependency-agent för Linux publiceras och stöds av Microsoft. Tillägget installerar beroendeagenten på virtuella Azure-datorer. Det här dokumentet beskriver de plattformar, konfigurationer och distributionsalternativ som stöds för azure VM Dependency agent virtual machine-tillägget för Linux.

## <a name="prerequisites"></a>Krav

### <a name="operating-system"></a>Operativsystem

Azure VM Dependency agent-tillägget för Linux kan köras mot de operativsystem som stöds i avsnittet [Operativsystem som stöds](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) i distributionsartikeln för Azure Monitor för virtuella datorer.

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för Azure VM Dependency agent-tillägget på en Azure Linux VM. 

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

| Namn | Värde/exempel |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| utgivare | Microsoft.Azure.Monitoring.DependencyAgent |
| typ | BeroendeAgentLinux |
| typHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Malldistribution

Du kan distribuera Azure VM-tillägg med Azure Resource Manager-mallar. Du kan använda JSON-schemat som beskrivs i föregående avsnitt i en Azure Resource Manager-mall för att köra azure VM Dependency agent-tillägget under en Azure Resource Manager-malldistribution.

JSON för en virtuell dator förlängning kan kapslas inuti den virtuella datorn resurs. Du kan också placera den på rot- eller toppnivå i en Resource Manager JSON-mall. Placeringen av JSON påverkar värdet för resursnamnet och resurstypen. Mer information finns i [Ange namn och typ för underordnade resurser](../../azure-resource-manager/templates/child-resource-name-type.md).

I följande exempel förutsätts att tillägget beroendeagent är kapslat i resursen för den virtuella datorn. När du kapslar tilläggsresursen placeras `"resources": []` JSON i objektet för den virtuella datorn.


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

När du placerar tillägget JSON i roten av mallen innehåller resursnamnet en referens till den överordnade virtuella datorn. Typen återspeglar den kapslade konfigurationen. 

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

Du kan använda Azure CLI för att distribuera VM-tillägget för beroendeagent till en befintlig virtuell dator.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för tilläggsdistributioner kan hämtas från Azure-portalen och med hjälp av Azure CLI. Om du vill se distributionstillståndet för tillägg för en viss virtuell dator kör du följande kommando med hjälp av Azure CLI:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Utdata för tilläggskörning loggas till följande fil:

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kontaktar du Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
