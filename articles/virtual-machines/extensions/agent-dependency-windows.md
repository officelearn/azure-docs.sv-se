---
title: Azure Monitor beroende virtuell dators tillägg för Windows | Microsoft Docs
description: Distribuera den Azure Monitor beroende agenten på en virtuell Windows-dator med hjälp av ett tillägg för virtuell dator.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: b6b1292e4c8874da826740d303cd0de26f9f46ee
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092759"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Azure Monitor beroende virtuell dators tillägg för Windows

I Azure Monitor för virtuella datorer kartan funktionen hämtar data från Microsoft Dependency agenten. Tillägget för virtuell dator i Azure VM-beroende agent för Windows publiceras och stöds av Microsoft. Tillägget installerar beroende agenten på virtuella Azure-datorer. Det här dokumentet innehåller information om de plattformar, konfigurationer och distributions alternativ som stöds för Azure VM-beroende agents tillägg för virtuell dator för Windows.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="operating-system"></a>Operativsystem

Tillägget Azure VM Dependency agent för Windows kan köras mot de operativ system som stöds i avsnittet [operativ system som stöds](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) i artikeln Azure Monitor for VMS distribution.

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för tillägget Azure VM Dependency agent på en virtuell Azure Windows-dator.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
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
        "type": "DependencyAgentWindows",
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
| type | DependencyAgentWindows |
| typeHandlerVersion | 9.5 |

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
        "type": "DependencyAgentWindows",
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
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>PowerShell-distribution

Du kan använda `Set-AzVMExtension` kommandot för att distribuera den beroende agentens virtuella dator tillägg till en befintlig virtuell dator. Innan du kör kommandot måste offentliga och privata konfigurationer lagras i en PowerShell hash-tabell.

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Data om tillstånd för tilläggs distributioner kan hämtas från Azure Portal och med hjälp av modulen Azure PowerShell. Om du vill se distributions statusen för tillägg för en virtuell dator kör du följande kommando med hjälp av Azure PowerShell-modulen:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Utökning av utdata loggas till filer som finns i följande katalog:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).
