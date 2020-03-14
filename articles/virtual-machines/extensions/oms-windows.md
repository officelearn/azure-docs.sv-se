---
title: Log Analytics tillägg för virtuell dator för Windows
description: Distribuera Log Analytics agenten på en virtuell Windows-dator med ett tillägg för virtuell dator.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/30/2020
ms.author: akjosh
ms.openlocfilehash: a0b5f45ba993033a9f56ee6b078193677b1c5aad
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253811"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Log Analytics tillägg för virtuell dator för Windows

Azure Monitor-loggar innehåller övervaknings funktioner över molnbaserade och lokala till gångar. Tillägget för virtuell dator med Log Analytics agent för Windows publiceras och stöds av Microsoft. Tillägget Log Analytics-agenten installeras på virtuella Azure-datorer och registreras virtuella datorer i en befintlig Log Analytics-arbetsyta. Det här dokumentet innehåller information om plattformar, konfigurationer och distributions alternativ som stöds för Log Analytics virtuell dator tillägg för Windows.

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

Mer information om Windows-operativsystem som stöds finns i artikeln [Log Analytics agent översikt](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) .

### <a name="agent-and-vm-extension-version"></a>Version av agenten och tillägg för virtuell dator
Följande tabell innehåller en mappning av versionen av Windows Log Analytics VM-tillägget och Log Analytics agent-paketet för varje version. 

| Log Analytics Windows agent-paket version | Log Analytics version av Windows VM-tillägg | Utgivningsdatum | Viktig information |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18018 | 1.0.18018 | Oktober 2019 | <ul><li> Mindre fel korrigeringar och stabiliserings förbättringar </li></ul> |
| 10.20.18011 | 1.0.18011 | Juli 2019 | <ul><li> Mindre fel korrigeringar och stabiliserings förbättringar </li><li> Ökat MaxExpressionDepth till 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Juni 2019 | <ul><li> Mindre fel korrigeringar och stabiliserings förbättringar </li><li> Möjlighet att inaktivera standardautentiseringsuppgifter vid anslutning av proxy (stöd för WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) har lagts till </li></ul>|
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

Följande JSON visar schemat för Log Analytics agent-tillägget. Tillägget kräver arbetsyte-ID och arbets ytans nyckel från mål Log Analyticss arbets ytan. Du hittar dem i inställningarna för arbets ytan i Azure Portal. Eftersom arbetsytenyckeln ska behandlas som känsliga data, ska den lagras i en skyddad Konfigurationsinställningen. Azure VM-tillägget skyddade inställningsdata krypteras och dekrypteras bara på den virtuella måldatorn. Observera att **workspaceId** och **workspaceKey** är Skift läges känsliga.

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
| typ | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (t) * | 6f680a37-00c6-41C7-a93f-1437e3462574 |
| workspaceKey (t.ex.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\* workspaceId kallas consumerId i Log Analytics-API: et.

> [!NOTE]
> För ytterligare egenskaper, se Azure [Connect Windows-datorer för att Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schemat som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra Log Analytics agent-tillägget under en distribution av Azure Resource Manager mallar. En exempel mall som innehåller Log Analytics agentens VM-tillägg finns i [Azure Snabbstart-galleriet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>Mallen har inte stöd för att ange mer än ett arbetsyte-ID och en arbetsyte nyckel när du vill konfigurera agenten att rapportera till flera arbets ytor. Om du vill konfigurera agenten att rapportera till flera arbets ytor, se [lägga till eller ta bort en arbets yta](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace).  

JSON för ett tillägg för virtuell dator kan kapslas i den virtuella dator resursen eller placeras på rot-eller toppnivå i en Resource Manager JSON-mall. Placeringen av JSON påverkar värdet för resurs namn och typ. Mer information finns i [Ange namn och typ för underordnade resurser](../../azure-resource-manager/templates/child-resource-name-type.md). 

I följande exempel antas Log Analytics-tillägget kapslas i den virtuella dator resursen. Vid kapsling av tilläggs resursen placeras JSON i `"resources": []`-objektet på den virtuella datorn.


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

`Set-AzVMExtension`-kommandot kan användas för att distribuera den virtuella datorn med Log Analytics agent-tillägget till en befintlig virtuell dator. Innan du kör kommandot måste offentliga och privata konfigurationer lagras i en PowerShell hash-tabell. 

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

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj få support. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).
