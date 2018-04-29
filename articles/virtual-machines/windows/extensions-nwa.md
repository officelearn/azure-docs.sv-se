---
title: Azure Network Watcher Agent virtuella tillägget för Windows | Microsoft Docs
description: Distribuera Network Watcher Agent på Windows virtuell dator med ett tillägg för virtuell dator.
services: virtual-machines-windows
documentationcenter: ''
author: dennisg
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: dcfbc22b0f3d6eac58a6a6e0c95dd5c1f8df3527
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Nätverk Watcher Agent tillägg för virtuell dator för Windows

## <a name="overview"></a>Översikt

[Azure Nätverksbevakaren](../../network-watcher/network-watcher-monitoring-overview.md) är en prestanda övervakning, diagnostik och analytics nätverkstjänst som tillåter övervakning av Azure-nätverk. Tillägget för virtuell dator Network Watcher Agent är ett krav för att samla in nätverkstrafik på begäran och andra avancerade funktioner på virtuella Azure-datorer.


Det här dokumentet beskriver de plattformar som stöds och distributionsalternativ för tillägget för virtuell dator Network Watcher Agent för Windows. Installationen av agenten inte störa eller kräver en omstart av den virtuella datorn.

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

Network Watcher Agent-tillägget utgåvor för Windows kan köras mot Windows Server 2008 R2, 2012 och 2012 R2 2016. Nano Server stöds inte.

### <a name="internet-connectivity"></a>Internetanslutning

Några av funktioner som Network Watcher Agent kräver att den virtuella måldatorn är ansluten till Internet. Utan möjlighet att upprätta utgående anslutningar, Network Watcher Agent inte kan hämta paketet insamlingar till ditt lagringskonto. Mer information finns i [Nätverksbevakaren dokumentationen](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för tillägget Network Watcher Agent. Tillägget varken kräver, eller stöder alla inställningar som anges av användaren, och bygger på en standardkonfiguration.

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

| Namn | Värdet / exempel |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| Publisher | Microsoft.Azure.NetworkWatcher |
| typ | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Malldistribution

Du kan distribuera Azure VM-tillägg med Azure Resource Manager-mallar. Du kan använda JSON-schema som beskrivs i föregående avsnitt i en Azure Resource Manager-mall för att köra tillägget Network Watcher Agent under en Azure Resource Manager för malldistribution.

## <a name="powershell-deployment"></a>PowerShell-distribution

Använd den `Set-AzureRmVMExtension` kommando för att distribuera Network Watcher Agent tillägget för virtuell dator till en befintlig virtuell dator:

```powershell
Set-AzureRmVMExtension `
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

Du kan hämta data om tillståndet för distributioner av tillägg från Azure portal och PowerShell. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando med hjälp av Azure PowerShell-modulen:

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

Tillägget utförande-utdatan loggas till filer som finns i följande katalog:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln finns i användarhandboken för nätverket Watcher dokumentationen eller kontakta Azure-experter på den [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du lagra en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välja Get support. Information om hur du använder Azure stöder finns i [vanliga frågor om Microsoft Azure-supporten](https://azure.microsoft.com/support/faq/).
