---
title: Virtuell dator-tillägget för Azure Network Watcher Agent för Windows
description: Distribuera Network Watcher Agent på windows virtuella dator med hjälp av en virtuell dator tillägg.
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: mimckitt
ms.openlocfilehash: f226e240a59b33c2913919495410b1a4923b4902
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261677"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Nätverksbevakningsagent för virtuell datortillägg för Windows

## <a name="overview"></a>Översikt

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) är en nätverksprestandaövervaknings-, diagnostik- och analystjänst som gör det möjligt att övervaka Azure-nätverk. Tillägget Network Watcher Agent för virtuella datorer är ett krav för att samla in nätverkstrafik på begäran och andra avancerade funktioner på virtuella Azure-datorer.


I det här dokumentet beskrivs de plattformar och distributionsalternativ som stöds för tillägget Nätverksbevakningsagent för Windows. Installationen av agenten stör inte, eller kräver en omstart, av den virtuella datorn. Du kan distribuera tillägget till virtuella datorer som du distribuerar. Om den virtuella datorn distribueras av en Azure-tjänst kan du läsa dokumentationen för tjänsten för att avgöra om den tillåter installation av tillägg på den virtuella datorn.

## <a name="prerequisites"></a>Krav

### <a name="operating-system"></a>Operativsystem

Tillägget Network Watcher Agent för Windows kan köras mot Windows Server 2008 R2, 2012, 2012 R2 och 2016.The Network Watcher Agent extension for Windows can be run against Windows Server 2008 R2, 2012, 2012 R2, and 2016 releases. Nano Server stöds inte just nu.

### <a name="internet-connectivity"></a>Internetanslutning

Vissa av network watcher-agentfunktionerna kräver att den virtuella måldatorn är ansluten till Internet. Utan möjligheten att upprätta utgående anslutningar kan nätverksbevakningsagenten inte ladda upp paketinsamlingar till ditt lagringskonto. Mer information finns i dokumentationen för [Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för tillägget Network Watcher Agent. Tillägget varken kräver eller stöder några inställningar som tillhandahålls av användaren och förlitar sig på standardkonfigurationen.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentWindows",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Egenskapsvärden

| Namn | Värde / Exempel |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| utgivare | Microsoft.Azure.NetworkWatcher |
| typ | NetworkWatcherAgentFönster |
| typHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Malldistribution

Du kan distribuera Azure VM-tillägg med Azure Resource Manager-mallar. Du kan använda JSON-schemat som beskrivs i föregående avsnitt i en Azure Resource Manager-mall för att köra tillägget Network Watcher Agent under en Azure Resource Manager-malldistribution.

## <a name="powershell-deployment"></a>PowerShell-distribution

Använd `Set-AzVMExtension` kommandot för att distribuera tillägget Nätverksbevakningsagent till en befintlig virtuell dator:

```powershell
Set-AzVMExtension `
  -ResourceGroupName "myResourceGroup1" `
  -Location "WestUS" `
  -VMName "myVM1" `
  -Name "networkWatcherAgent" `
  -Publisher "Microsoft.Azure.NetworkWatcher" `
  -Type "NetworkWatcherAgentWindows" `
  -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>Felsökning och support

### <a name="troubleshooting"></a>Felsökning

Du kan hämta data om tillståndet för tilläggsdistributioner från Azure-portalen och PowerShell. Om du vill se distributionstillståndet för tillägg för en viss virtuell dator kör du följande kommando med Azure PowerShell-modulen:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

Utdata för tilläggskörning loggas till filer som finns i följande katalog:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du läsa dokumentationen till användarhandboken för Network Watcher eller kontakta Azure-experterna på [forumen FÖR MSDN Azure och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj Hämta support. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
