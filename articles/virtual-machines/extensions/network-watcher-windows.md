---
title: Tillägg för virtuell Azure Network Watcher agent-dator för Windows
description: Distribuera Network Watcher agenten på en virtuell Windows-dator med ett tillägg för virtuell dator.
services: virtual-machines-windows
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 998e160edce25b9d466a1db090abcefeb7870172
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073691"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Network Watcher virtuell agent för virtuella datorer för Windows

## <a name="overview"></a>Översikt

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) är en övervaknings-, diagnostik-och analys tjänst för nätverks prestanda som gör det möjligt att övervaka Azure-nätverk. Tillägget Network Watcher agent virtuell dator måste vara ett krav för att fånga in nätverks trafik på begäran och andra avancerade funktioner på virtuella Azure-datorer.


Det här dokumentet innehåller information om vilka plattformar och distributions alternativ som stöds för Network Watcher agentens tillägg för virtuell dator för Windows. Installationen av agenten störs inte eller kräver en omstart av den virtuella datorn. Du kan distribuera tillägget till virtuella datorer som du distribuerar. Om den virtuella datorn distribueras av en Azure-tjänst kan du läsa dokumentationen för tjänsten för att avgöra om den tillåter att installera tillägg på den virtuella datorn.

## <a name="prerequisites"></a>Krav

### <a name="operating-system"></a>Operativsystem

Network Watcher agent-tillägget för Windows kan köras mot Windows Server 2008 R2, 2012, 2012 R2 och 2016 versioner. Nano Server stöds inte för tillfället.

### <a name="internet-connectivity"></a>Internetanslutning

Vissa av de Network Watcher agent funktionerna kräver att den virtuella mål datorn är ansluten till Internet. Om du inte kan upprätta utgående anslutningar kan Network Watcher-agenten inte överföra paket avbildningar till ditt lagrings konto. Mer information finns i [Network Watcher-dokumentationen](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för Network Watcher agent-tillägget. Tillägget kräver inte, eller stöder inte, alla användarspecifika inställningar och förlitar sig på dess standard konfiguration.

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
| publisher | Microsoft.Azure.NetworkWatcher |
| typ | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Malldistribution

Du kan distribuera virtuella Azure-tillägg med Azure Resource Manager mallar. Du kan använda JSON-schemat som beskrivs i föregående avsnitt i en Azure Resource Manager-mall för att köra tillägget Network Watcher agent under en distribution av Azure Resource Manager mallar.

## <a name="powershell-deployment"></a>PowerShell-distribution

Använd kommandot `Set-AzVMExtension` för att distribuera tillägget Network Watcher virtuell dator för virtuella datorer till en befintlig virtuell dator:

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

Du kan hämta data om statusen för tillägg distributioner från Azure Portal och PowerShell. Om du vill se distributions statusen för tillägg för en virtuell dator kör du följande kommando med hjälp av Azure PowerShell-modulen:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

Utökning av utdata loggas till filer som finns i följande katalog:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du läsa dokumentationen för Network Watcher Användar handbok eller kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
