---
title: Azure Network Watcher Agent virtuell dator tillägg för Linux
description: Distribuera Network Watcher Agent på virtuella Linux-datorer med hjälp av ett tillägg för virtuella datorer.
services: virtual-machines-linux
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 712ec177996cd54d7bd6d184fea306009b58b083
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531030"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Virtuell dator-tillägget för Network Watcher Agent för Linux

## <a name="overview"></a>Översikt

[Azure Network Watcher](/azure/network-watcher/) är en nätverksprestandaövervaknings-, diagnostik- och analystjänst som möjliggör övervakning av Azure-nätverk. Tillägget Network Watcher Agent virtual machine (VM) är ett krav för vissa network watcher-funktioner på virtuella Azure-datorer, till exempel att samla in nätverkstrafik på begäran och andra avancerade funktioner.

I den här artikeln beskrivs de plattformar och distributionsalternativ som stöds för VM-tillägget Network Watcher Agent för Linux. Installationen av agenten stör inte, eller kräver en omstart, av den virtuella datorn. Du kan distribuera tillägget till virtuella datorer som du distribuerar. Om den virtuella datorn distribueras av en Azure-tjänst kan du läsa dokumentationen för tjänsten för att avgöra om den tillåter installation av tillägg på den virtuella datorn.

## <a name="prerequisites"></a>Krav

### <a name="operating-system"></a>Operativsystem

Tillägget Network Watcher Agent kan konfigureras för följande Linux-distributioner:

| Distribution | Version |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 och 8 |
| Red Hat | 6 och 7 |
| Oracle Linux | 6,8+ och 7 |
| SUSE Linux Enterprise Server | 11 och 12 |
| OpenSUSE språng | 42,3+ |
| CentOS | 6,5+ och 7 |
| CoreOS | 899.17.0+ |


### <a name="internet-connectivity"></a>Internetanslutning

Vissa av network watcher-agentfunktionerna kräver att en virtuell dator är ansluten till Internet. Utan möjlighet att upprätta utgående anslutningar kan vissa nätverksbevakningsagentfunktioner sluta fungera eller bli otillgängliga. Mer information om nätverksbevakningsfunktioner som kräver agenten finns i dokumentationen för[Network Watcher](/azure/network-watcher/).

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för tillägget Network Watcher Agent. Tillägget kräver inte, eller stöder, några inställningar som tillhandahålls av användaren. Tillägget är beroende av standardkonfigurationen.

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

| Namn | Värde / Exempel |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| utgivare | Microsoft.Azure.NetworkWatcher |
| typ | NätverkWatcherAgentLinux |
| typHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Malldistribution

Du kan distribuera Azure VM-tillägg med en Azure Resource Manager-mall. Om du vill distribuera tillägget Network Watcher Agent använder du det tidigare json-schemat i mallen.

## <a name="azure-classic-cli-deployment"></a>Azure klassisk CLI-distribution

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

I följande exempel distribueras VM-tillägget för Nätverksbevakningsagenten till en befintlig virtuell dator som distribueras via den klassiska distributionsmodellen:

```console
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

I följande exempel distribueras VM-tillägget för Nätverksbevakningsagenten till en befintlig virtuell dator som distribueras via Resource Manager:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Felsökning och support

### <a name="troubleshooting"></a>Felsökning

Du kan hämta data om tillståndet för tilläggsdistributioner med antingen Azure-portalen eller Azure CLI.

I följande exempel visas distributionstillståndet för NetworkWatcherAgentLinux-tillägget för en virtuell dator som distribueras via Resource Manager med hjälp av Azure CLI:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du läsa dokumentationen för [Network Watcher](/azure/network-watcher/)eller kontakta Azure-experterna på [FORUMEN MSDN Azure och Stack Overflow](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
