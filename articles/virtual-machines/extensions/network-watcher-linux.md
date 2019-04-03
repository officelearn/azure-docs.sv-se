---
title: Azure Network Watcher-Agent VM-tillägg för Linux | Microsoft Docs
description: Distribuera Network Watcher-Agent på Linux-dator med hjälp av tillägg för virtuell dator.
services: virtual-machines-linux
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 5ed5e791cd6e611218769650115c78afd1869f67
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879020"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Network Watcher-Agent-tillägg för virtuell dator för Linux

## <a name="overview"></a>Översikt

[Azure Network Watcher](/azure/network-watcher/) är en prestanda övervakning, diagnostik och analys nätverkstjänst som tillåter övervakning för Azure-nätverk. Tillägget för virtuell dator (VM) Network Watcher-Agent är ett krav för några av Network Watcher-funktioner på virtuella Azure-datorer, till exempel samla in nätverkstrafik på begäran och andra avancerade funktioner.

Den här artikeln beskriver de plattformar som stöds och distributionsalternativ för Network Watcher-Agent VM-tillägget för Linux. Installationen av agenten inte störa eller kräver en omstart av den virtuella datorn. Du kan distribuera tillägget till virtuella datorer som du distribuerar. Om den virtuella datorn distribueras av en Azure-tjänst kan du i dokumentationen för tjänsten att avgöra huruvida den tillåter att installera tillägg i den virtuella datorn.

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

Network Watcher-Agent-tillägget kan konfigureras för följande Linux-distributioner:

| Distribution | Version |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 och 8 |
| Red Hat | 6 och 7 |
| Oracle Linux | 6.8 + och 7 |
| SUSE Linux Enterprise Server | 11 och 12 |
| OpenSUSE Leap | 42.3+ |
| CentOS | 6.5 + och 7 |
| CoreOS | 899.17.0+ |


### <a name="internet-connectivity"></a>Internetanslutning

Några av Network Watcher-Agent-funktioner kräver att en virtuell dator är ansluten till Internet. Utan möjlighet att upprätta utgående anslutningar, några av funktionerna i Network Watcher-Agent fungera eller blir otillgänglig. Mer information om Network Watcher-funktioner som kräver att agenten finns i den[dokumentation om Network Watcher](/azure/network-watcher/).

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för Network Watcher-Agent-tillägget. Tillägget inte kräver eller stöder alla inställningar som anges av användaren. Tillägget är beroende av en standardkonfiguration.

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
        "type": "NetworkWatcherAgentLinux",
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
| typ | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Malldistribution

Du kan distribuera Azure VM-tillägg med en Azure Resource Manager-mall. Använd föregående json-schemat i mallen för att distribuera Network Watcher-Agent-tillägget.

## <a name="azure-classic-cli-deployment"></a>Klassiska Azure CLI-distributionen

I följande exempel distribueras Network Watcher-Agent VM-tillägget till en befintlig virtuell dator distribueras via den klassiska distributionsmodellen:

```azurecli
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

I följande exempel distribueras Network Watcher-Agent VM-tillägget till en befintlig virtuell dator distribueras via Resource Manager:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Felsökning och support

### <a name="troubleshooting"></a>Felsökning

Du kan hämta data om tillståndet för tillägget för distributioner med hjälp av Azure portal eller Azure CLI.

I följande exempel visas distributionsstatusen för tillägg för en virtuell dator distribueras via den klassiska distributionsmodellen med hjälp av Azure klassiska CLI:

```azurecli
azure config mode asm
azure vm extension get myVM1
```
Tillägget utförande-utdatan loggas till filer som finns i följande katalog:

```
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
```

I följande exempel visas distributionsstatusen för tillägget NetworkWatcherAgentLinux för en virtuell dator distribueras via Resource Manager med hjälp av Azure-CLI:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du referera till den [dokumentation om Network Watcher](/azure/network-watcher/), eller kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **få support**. Information om hur du använder Azure-supporten finns i den [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
