---
title: Azure Network Watcher-Agent VM-tillägget för Windows | Microsoft Docs
description: Distribuera Network Watcher-Agent på Windows-dator med tillägg för virtuell dator.
services: virtual-machines-windows
documentationcenter: ''
author: gurudennis
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
ms.openlocfilehash: 2f07107ad63ddd04e67528bf4f409dabf4a4d0c0
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42056754"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Network Watcher-Agent VM-tillägg för Windows

## <a name="overview"></a>Översikt

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) är en prestanda övervakning, diagnostik och analys nätverkstjänst som tillåter övervakning av Azure-nätverk. Network Watcher-Agent-tillägget för virtuell dator är ett krav för att samla in nätverkstrafik på begäran och andra avancerade funktioner i Azure virtual machines.


Det här dokumentet beskriver de plattformar som stöds och distributionsalternativ för Network Watcher-Agent-tillägget för virtuell dator för Windows. Installationen av agenten inte störa eller kräver en omstart av den virtuella datorn. Du kan distribuera tillägget till virtuella datorer som du distribuerar. Om den virtuella datorn distribueras av en Azure-tjänst kan du i dokumentationen för tjänsten att avgöra huruvida den tillåter att installera tillägg i den virtuella datorn.

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

Network Watcher-Agent-tillägget versioner för Windows kan köras mot Windows Server 2008 R2, 2012, 2012 R2 och 2016. Nano Server stöds inte just nu.

### <a name="internet-connectivity"></a>Internetanslutning

Några av Network Watcher-Agent-funktioner kräver att den virtuella måldatorn är ansluten till Internet. Network Watcher-Agent kan inte överföra infångade paket till ditt lagringskonto utan möjlighet att upprätta utgående anslutningar. Mer information finns i den [dokumentation om Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för Network Watcher-Agent-tillägget. Tillägget varken kräver, inte heller stöder, alla inställningar som anges av användaren, och förlitar sig på standardkonfigurationen.

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
| utgivare | Microsoft.Azure.NetworkWatcher |
| typ | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Malldistribution

Du kan distribuera Azure VM-tillägg med Azure Resource Manager-mallar. Du kan använda JSON-schemat som beskrivs i föregående avsnitt i en Azure Resource Manager-mall för att köra tillägget Network Watcher-Agent under en malldistribution för Azure Resource Manager.

## <a name="powershell-deployment"></a>PowerShell-distribution

Använd den `Set-AzureRmVMExtension` kommando för att distribuera Network Watcher-Agent-tillägget för virtuell dator till en befintlig virtuell dator:

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

Du kan hämta data om tillståndet för distributioner av tillägg från Azure-portalen och PowerShell. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando med hjälp av Azure PowerShell-modulen:

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

Tillägget utförande-utdatan loggas till filer som finns i följande katalog:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln finns i användarhandboken för Network Watcher-dokumentationen eller kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
