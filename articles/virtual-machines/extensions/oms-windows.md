---
title: Azure Monitor VM-tillägget för Windows | Microsoft Docs
description: Distribuera Log Analytics-agenten på Windows-dator med tillägg för virtuell dator.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/29/2019
ms.author: roiyz
ms.openlocfilehash: 2287a0c39a82509e21ff35d8c3786cf1c85b1b24
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142884"
---
# <a name="azure-monitor-virtual-machine-extension-for-windows"></a>Azure Monitor VM-tillägget för Windows

Azure Monitor-loggar ger övervakningsfunktionerna i molnet och lokala tillgångar. Log Analytics-agenten VM-tillägget för Windows är publicerat och stöds av Microsoft. Tillägget Log Analytics-agenten installeras på virtuella Azure-datorer och registreras virtuella datorer i en befintlig Log Analytics-arbetsyta. Det här dokumentet beskriver de plattformar som stöds, konfigurationer och distributionsalternativen för Azure Monitor-tillägget för virtuell dator för Windows.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="operating-system"></a>Operativsystem

Log Analytics-agenttillägg för Windows stöder följande versioner av Windows-operativsystem:

- Windows Server 2019
- Windows Server 2008 R2, 2012, 2012 R2, 2016, version 1709 och 1803

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center automatiskt etablerar Log Analytics-agenten och ansluter den med standard Log Analytics-arbetsyta för Azure-prenumerationen. Om du använder Azure Security Center kan inte köra stegen i det här dokumentet. Detta skriver över den konfigurerade arbetsytan och bryta länken med Azure Security Center.

### <a name="internet-connectivity"></a>Internetanslutning
Log Analytics-agenttillägg för Windows kräver att den virtuella måldatorn är ansluten till internet. 

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för Log Analytics-agenttillägg. Tillägget kräver arbetsytans ID och arbetsytenyckel från Log Analytics-målarbetsytan. Dessa kan hittas i inställningarna för arbetsytan på Azure-portalen. Eftersom arbetsytenyckeln ska behandlas som känsliga data, ska den lagras i en skyddad Konfigurationsinställningen. Azure VM-tillägget skyddade inställningsdata krypteras och dekrypteras bara på den virtuella måldatorn. Observera att **workspaceId** och **workspaceKey** är skiftlägeskänsliga.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Egenskapsvärden

| Namn | Värdet / exempel |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| utgivare | Microsoft.EnterpriseCloud.Monitoring |
| typ | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (e.g)* | 6f680a37-00c6-41C7-a93f-1437e3462574 |
| workspaceKey (t.ex.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\* WorkspaceId kallas consumerId i Log Analytics-API.

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schemat som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra tillägget för Log Analytics-agent under en malldistribution för Azure Resource Manager. En exempelmall som innehåller VM-tillägg för Log Analytics-agenten finns på den [Azure Quick Start-galleriet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>Mallen har inte stöd för att ange mer än en arbetsyte-ID och arbetsytenyckel när du vill konfigurera agenten för att rapportera till flera arbetsytor. För att konfigurera agenten till flera arbetsytor, se [när du lägger till eller ta bort en arbetsyta](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace).  

JSON för tillägg för virtuell dator kan kapslas i resursen för virtuella datorer eller placeras i roten eller översta nivån i en Resource Manager JSON-mall. Placeringen av JSON påverkar värdet för resursnamn och typ. Mer information finns i [ange namn och typ för underordnade resurser](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources). 

I följande exempel förutsätter att tillägget Azure Monitor är kapslade i den virtuella datorresursen. När kapsla tillägget resursen JSON placeras i den `"resources": []` objekt av den virtuella datorn.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

När du monterar tillägget JSON i roten på mallen resursnamnet innehåller en referens till den överordnade virtuella datorn och typen återspeglar den kapslade konfigurationen. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>PowerShell-distribution

Den `Set-AzVMExtension` kommando kan användas för att distribuera Log Analytics agent-tillägget för virtuell dator till en befintlig virtuell dator. Innan du kör kommandot, måste de offentliga och privata konfigurationerna lagras i en PowerShell-hash-tabell. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och med hjälp av Azure PowerShell-modulen. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando med hjälp av Azure PowerShell-modulen.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Tillägget utförande-utdatan loggas till filer som finns i följande katalog:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
