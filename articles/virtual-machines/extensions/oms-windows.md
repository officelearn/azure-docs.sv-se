---
title: Azure Monitor tillägg för virtuell dator för Windows | Microsoft Docs
description: Distribuera Log Analytics agenten på en virtuell Windows-dator med ett tillägg för virtuell dator.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2019
ms.author: roiyz
ms.openlocfilehash: 700d8c6ea1527598591aa4300a977f80085e04b0
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990370"
---
# <a name="azure-monitor-virtual-machine-extension-for-windows"></a>Azure Monitor tillägg för virtuell dator för Windows

Azure Monitor-loggar innehåller övervaknings funktioner över molnbaserade och lokala till gångar. Tillägget för virtuell dator med Log Analytics agent för Windows publiceras och stöds av Microsoft. Tillägget Log Analytics-agenten installeras på virtuella Azure-datorer och registreras virtuella datorer i en befintlig Log Analytics-arbetsyta. Det här dokumentet innehåller information om plattformar, konfigurationer och distributions alternativ som stöds för Azure Monitor virtuell dator tillägg för Windows.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

Mer information om Windows-operativsystem som stöds finns i artikeln [Log Analytics agent översikt](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) .

### <a name="agent-and-vm-extension-version"></a>Version av agenten och tillägg för virtuell dator
Följande tabell innehåller en mappning av versionen av Windows Azure Monitor VM-tillägget och Log Analytics agent-paketet för varje version. 

| Log Analytics Windows agent-paket version | Azure Monitor version av Windows VM-tillägg | Utgivningsdatum | Viktig information |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18011 | 1.0.18011 | Juli 2019 | <ul><li> Mindre fel korrigeringar och stabiliserings förbättringar </li><li> Ökat MaxExpressionDepth till 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | 2019 juni | <ul><li> Mindre fel korrigeringar och stabiliserings förbättringar </li><li> Möjlighet att inaktivera standardautentiseringsuppgifter när du skapar en proxy-anslutning (stöd för WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) har lagts till </li></ul>|
| 10.19.13515 | 1.0.13515 | Mars 2019 | <ul><li>Mindre stabiliserings korrigeringar </li></ul> |
| 10.19.10006 | Saknas | Dec 2018 | <ul><li> Mindre stabiliserings korrigeringar </li></ul> | 
| 8.0.11136 | Saknas | Sept 2018 |  <ul><li> Stöd har lagts till för att identifiera resurs-ID-ändring vid flyttning av virtuell dator </li><li> Stöd har lagts till för rapporterings resurs-ID vid användning av installation utan tillägg </li></ul>| 
| 8.0.11103 | Saknas |  April 2018 | |
| 8.0.11081 | 1.0.11081 | Nov 2017 | | 
| 8.0.11072 | 1.0.11072 | Sept 2017 | |
| 8.0.11049 | 1.0.11049 | Feb 2017 | |

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center etablerar automatiskt Log Analytics agenten och ansluter den till standard arbets ytan för Log Analytics i Azure-prenumerationen. Om du använder Azure Security Center kan inte köra stegen i det här dokumentet. Om du gör det skrivs den konfigurerade arbets ytan över och anslutningen till Azure Security Center bryts.

### <a name="internet-connectivity"></a>Internetanslutning
Log Analytics agent-tillägget för Windows kräver att den virtuella mål datorn är ansluten till Internet. 

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för Log Analytics agent-tillägget. Tillägget kräver arbetsyte-ID och arbets ytans nyckel från mål Log Analyticss arbets ytan. Du hittar dem i inställningarna för arbets ytan i Azure Portal. Eftersom arbetsytenyckeln ska behandlas som känsliga data, ska den lagras i en skyddad Konfigurationsinställningen. Azure VM-tillägget skyddade inställningsdata krypteras och dekrypteras bara på den virtuella måldatorn. Observera att **workspaceId** och **workspaceKey** är skiftlägeskänsliga.

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
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (t) * | 6f680a37-00c6-41C7-a93f-1437e3462574 |
| workspaceKey (t.ex.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\*WorkspaceId kallas för consumerId i Log Analytics-API: et.

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schemat som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra Log Analytics agent-tillägget under en distribution av Azure Resource Manager mallar. En exempel mall som innehåller Log Analytics agentens VM-tillägg finns i [Azure Snabbstart](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm)-galleriet. 

>[!NOTE]
>Mallen har inte stöd för att ange mer än ett arbetsyte-ID och en arbetsyte nyckel när du vill konfigurera agenten att rapportera till flera arbets ytor. Om du vill konfigurera agenten att rapportera till flera arbets ytor, se [lägga till eller ta bort en arbets yta](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace).  

JSON för ett tillägg för virtuell dator kan kapslas i den virtuella dator resursen eller placeras på rot-eller toppnivå i en Resource Manager JSON-mall. Placeringen av JSON påverkar värdet för resurs namn och typ. Mer information finns i [ange namn och typ för underordnade resurser](../../azure-resource-manager/child-resource-name-type.md). 

I följande exempel antas Azure Monitor-tillägget kapslas i den virtuella dator resursen. När kapsla tillägget resursen JSON placeras i den `"resources": []` objekt av den virtuella datorn.


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

`Set-AzVMExtension` Kommandot kan användas för att distribuera tillägget Log Analytics agent virtuell dator till en befintlig virtuell dator. Innan du kör kommandot måste offentliga och privata konfigurationer lagras i en PowerShell hash-tabell. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
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

Data om tillstånd för tilläggs distributioner kan hämtas från Azure Portal och med hjälp av modulen Azure PowerShell. Om du vill se distributions statusen för tillägg för en virtuell dator kör du följande kommando med hjälp av Azure PowerShell-modulen.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Utökning av utdata loggas till filer som finns i följande katalog:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
