---
title: Log Analytics-tillägg för virtuella datorer för Windows
description: Distribuera Log Analytics-agenten på den virtuella Windows-datorn med hjälp av ett tillägg för virtuella datorer.
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
ms.openlocfilehash: 85977819d30ddc8745eb9231242eb1990222676c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530996"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Log Analytics-tillägg för virtuella datorer för Windows

Azure Monitor Logs tillhandahåller övervakningsfunktioner över moln- och lokala resurser. Tillägget Log Analytics-agent för Windows publiceras och stöds av Microsoft. Tillägget installerar Log Analytics-agenten på virtuella Azure-datorer och registrerar virtuella datorer till en befintlig Log Analytics-arbetsyta. I det här dokumentet beskrivs de plattformar, konfigurationer och distributionsalternativ som stöds för tillägget Log Analytics för virtuella datorer i Windows.

## <a name="prerequisites"></a>Krav

### <a name="operating-system"></a>Operativsystem

Mer information om windows-operativsystem som stöds finns i översiktsartikeln [för Log Analytics-agenten.](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)

### <a name="agent-and-vm-extension-version"></a>Agent- och VM-tilläggsversion
Följande tabell innehåller en mappning av versionen av tillägget Windows Log Analytics VM och Log Analytics-agentpaketet för varje utgåva. 

| Logg Analytics Windows agent paketversion | Logganalys Windows VM-tilläggsversion | Utgivningsdatum | Viktig information |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18029 | 1.0.18029 | Mars 2020   | <ul><li>Lägger till stöd för SIGNering av SHA-2-kod</li><li>Förbättrar installation och hantering av vm-tillägg</li><li>Löser ett fel i Azure Arc for Servers-integrering</li><li>Lägger till ett inbyggt felsökningsverktyg för kundsupport</li><li>Lägger till stöd för ytterligare Azure Government-regioner</li> |
| 10.20.18018 | 1.0.18018 | Oktober 2019 | <ul><li> Mindre buggfixar och stabiliseringsförbättringar </li></ul> |
| 10.20.18011 | 1.0.18011 | Juli 2019 | <ul><li> Mindre buggfixar och stabiliseringsförbättringar </li><li> Ökad MaxExpressionDepth till 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Juni 2019 | <ul><li> Mindre buggfixar och stabiliseringsförbättringar </li><li> Lade till möjligheten att inaktivera standardautentiseringsuppgifter när proxyanslutningen upprättas (stöd för WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) </li></ul>|
| 10.19.13515 | 1.0.13515 | Mars 2019 | <ul><li>Mindre stabiliseringskorrigeringar </li></ul> |
| 10.19.10006 | Saknas | Dec 2018 | <ul><li> Mindre stabiliseringskorrigeringar </li></ul> | 
| 8.0.11136 | Saknas | September 2018 |  <ul><li> Lagt till stöd för att identifiera resurs-ID-ändring på vm-flytt </li><li> Lade till stöd för att rapportera resurs-ID när du använder installation av icke-tillägg </li></ul>| 
| 8.0.11103 | Saknas |  April 2018 | |
| 8.0.11081 | 1.0.11081 | Nov 2017 | | 
| 8.0.11072 | 1.0.11072 | September 2017 | |
| 8.0.11049 | 1.0.11049 | Februari 2017 | |


### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center etablerar automatiskt Log Analytics-agenten och ansluter den till standardarbetsytan Log Analytics för Azure-prenumerationen. Om du använder Azure Security Center ska du inte köra igenom stegen i det här dokumentet. Om du gör det skrivs den konfigurerade arbetsytan över och anslutningen till Azure Security Center bryts.

### <a name="internet-connectivity"></a>Internetanslutning
Tillägget Log Analytics-agent för Windows kräver att den virtuella måldatorn är ansluten till internet. 

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för agenttillägget Log Analytics. Tillägget kräver arbetsyte-ID och arbetsytan från mållogganalysarbetsytan. Dessa finns i inställningarna för arbetsytan i Azure-portalen. Eftersom arbetsytans nyckel ska behandlas som känsliga data bör den lagras i en skyddad inställningskonfiguration. Azure VM-tilläggsskyddade inställningsdata krypteras och dekrypteras endast på den virtuella måldatorn. Observera att **workspaceId** och **workspaceKey** är skiftlägeskänsliga.

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

| Namn | Värde / Exempel |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| utgivare | Övervakning av Microsoft.EnterpriseCloud. |
| typ | MicrosoftMonitoringAgent |
| typHandlerVersion | 1.0 |
| arbetsytaId (t.ex.) * | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (t.ex.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\*WorkspaceId kallas consumerId i Log Analytics API.

> [!NOTE]
> Ytterligare egenskaper finns i Azure [Connect Windows-datorer till Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schemat som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra tillägget Log Analytics-agent under en Azure Resource Manager-malldistribution. En exempelmall som innehåller vm-tillägget Log Analytics-agent finns i [Azure Quickstart Gallery](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>Mallen stöder inte att ange mer än en arbetsyte-ID och arbetsyta när du vill konfigurera agenten så att den rapporterar till flera arbetsytor. Mer om du vill konfigurera agenten så att den rapporterar till flera arbetsytor finns i [Lägga till eller ta bort en arbetsyta](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace).  

JSON för en virtuell datortillägg kan kapslas inuti den virtuella datorn resurs, eller placeras på roten eller den översta nivån i en Resource Manager JSON-mall. Placeringen av JSON påverkar värdet för resursnamnet och resurstypen. Mer information finns i [Ange namn och typ för underordnade resurser](../../azure-resource-manager/templates/child-resource-name-type.md). 

I följande exempel förutsätts att Log Analytics-tillägget är kapslat i resursen för den virtuella datorn. När tilläggsresursen kapslas placeras JSON i objektet för `"resources": []` den virtuella datorn.


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

När du placerar tillägget JSON i roten av mallen innehåller resursnamnet en referens till den överordnade virtuella datorn och typen återspeglar den kapslade konfigurationen. 

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

Kommandot `Set-AzVMExtension` kan användas för att distribuera tillägget Log Analytics-agent för virtuella datorer till en befintlig virtuell dator. Innan kommandot körs måste de offentliga och privata konfigurationerna lagras i en PowerShell-hash-tabell. 

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

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för tilläggsdistributioner kan hämtas från Azure-portalen och med hjälp av Azure PowerShell-modulen. Om du vill se distributionstillståndet för tillägg för en viss virtuell dator kör du följande kommando med Azure PowerShell-modulen.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Utdata för tilläggskörning loggas till filer som finns i följande katalog:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj Hämta support. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
