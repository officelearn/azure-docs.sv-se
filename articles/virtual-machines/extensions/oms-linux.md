---
title: Log Analytics-tillägg för virtuella datorer för Linux
description: Distribuera Log Analytics-agenten på virtuell Linux-dator med hjälp av ett tillägg för virtuella datorer.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: akjosh
ms.openlocfilehash: 9ddac229fc38a91a8b97b24dc2807080b2295758
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250561"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>Log Analytics-tillägg för virtuella datorer för Linux

## <a name="overview"></a>Översikt

Azure Monitor Logs tillhandahåller övervaknings-, aviserings- och aviseringsreparationsfunktioner över moln- och lokala resurser. Tillägget Log Analytics för virtuella datorer för Linux publiceras och stöds av Microsoft. Tillägget installerar Log Analytics-agenten på virtuella Azure-datorer och registrerar virtuella datorer till en befintlig Log Analytics-arbetsyta. I det här dokumentet beskrivs de plattformar, konfigurationer och distributionsalternativ som stöds för tillägget Log Analytics för virtuella datorer för Linux.

>[!NOTE]
>Som en del av den pågående övergången från Microsoft Operations Management Suite (OMS) till Azure Monitor kommer OMS-agenten för Windows eller Linux att kallas Log Analytics-agenten för Windows och Log Analytics-agenten för Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Krav

### <a name="operating-system"></a>Operativsystem

Mer information om linuxdistributioner som stöds finns i översiktsartikeln [för Log Analytics-agenten.](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

### <a name="agent-and-vm-extension-version"></a>Agent- och VM-tilläggsversion
Följande tabell innehåller en mappning av versionen av log analytics VM-tillägget och Log Analytics-agentpaketet för varje utgåva. En länk till viktig information för Log Analytics-agentpaketversionen ingår. Viktig information innehåller information om buggfixar och nya funktioner som är tillgängliga för en viss agentrelease.  

| Log Analytics Linux VM-tilläggsversion | Logg Analytics Agent-paketversion | 
|--------------------------------|--------------------------|
| 1.12.25 | [1.12.15-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.12.15-0) |
| 1.11.15 | [1.11.0-9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
| 1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0-256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2-125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center etablerar automatiskt Log Analytics-agenten och ansluter den till en standardarbetsyta för Log Analytics som skapats av ASC i din Azure-prenumeration. Om du använder Azure Security Center ska du inte köra igenom stegen i det här dokumentet. Om du gör det skrivs den konfigurerade arbetsytan över och anslutningen till Azure Security Center bryts.

### <a name="internet-connectivity"></a>Internetanslutning

Log Analytics Agent-tillägget för Linux kräver att målvirvatorn är ansluten till internet. 

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för tillägget Log Analytics Agent. Tillägget kräver arbetsyte-ID och arbetsytan från mållogganalysarbetsytan. Dessa värden [finns på din Log Analytics-arbetsyta](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key) i Azure-portalen. Eftersom arbetsytans nyckel ska behandlas som känsliga data bör den lagras i en skyddad inställningskonfiguration. Azure VM-tilläggsskyddade inställningsdata krypteras och dekrypteras endast på den virtuella måldatorn. Observera att **workspaceId** och **workspaceKey** är skiftlägeskänsliga.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>Schemat ovan förutsätter att det kommer att placeras på rotnivån i mallen. Om du placerar den i den virtuella `type` `name` datorns resurs i mallen bör egenskaperna och ändras, enligt beskrivningen [längre ner](#template-deployment).

### <a name="property-values"></a>Egenskapsvärden

| Namn | Värde / Exempel |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| utgivare | Övervakning av Microsoft.EnterpriseCloud. |
| typ | OmsAgentForLinux |
| typHandlerVersion | 1.7 |
| workspaceId (t.ex.) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (t.ex.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Mallar är idealiska när du distribuerar en eller flera virtuella datorer som kräver postdistributionskonfiguration, till exempel introduktion till Azure Monitor Logs. En exempelmall för Resource Manager som innehåller tillägget Log Analytics Agent VM finns i [Azure Quickstart Gallery](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

JSON-konfigurationen för ett tillägg för virtuella datorer kan kapslas inuti resursen för den virtuella datorn eller placeras på rot- eller toppnivå i en Resource Manager JSON-mall. Placeringen av JSON-konfigurationen påverkar värdet för resursnamnet och resurstypen. Mer information finns i [Ange namn och typ för underordnade resurser](../../azure-resource-manager/templates/child-resource-name-type.md). 

I följande exempel förutsätts att vm-tillägget är kapslat i resursen för den virtuella datorn. När tilläggsresursen kapslas placeras JSON i objektet för `"resources": []` den virtuella datorn.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

När du placerar tillägget JSON i roten av mallen innehåller resursnamnet en referens till den överordnade virtuella datorn och typen återspeglar den kapslade konfigurationen.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.7",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att distribuera VM-tillägget Log Analytics Agent till en befintlig virtuell dator. Ersätt *värdet myWorkspaceKey* nedan med arbetsytenyckeln och *myWorkspaceId-värdet* med ditt arbetsyte-ID. Dessa värden finns på arbetsytan Log Analytics i Azure-portalen under *Avancerade inställningar*. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.10.1 --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för tilläggsdistributioner kan hämtas från Azure-portalen och med hjälp av Azure CLI. Om du vill se distributionstillståndet för tillägg för en viss virtuell dator kör du följande kommando med Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Utdata för tilläggskörning loggas till följande fil:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Felkoder och deras betydelser

| Felkod | Betydelse | Möjliga åtgärder |
| :---: | --- | --- |
| 9 | Aktivera anropad i förtid | [Uppdatera Azure Linux-agenten](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) till den senaste tillgängliga versionen. |
| 10 | Den virtuella datorn är redan ansluten till en Log Analytics-arbetsyta | Om du vill ansluta den virtuella datorn till den arbetsyta som anges i tilläggsschemat ställer du in stopOnMultipleConnections till false i offentliga inställningar eller tar bort den här egenskapen. Den här virtuella datorn faktureras en gång för varje arbetsyta som den är ansluten till. |
| 11 | Ogiltig config till tillägget | Följ föregående exempel för att ange alla egenskapsvärden som krävs för distributionen. |
| 17 | Logg analytics-paketinstallationsfel | 
| 19 | Installationsfel för OMI-paket | 
| 20 | Installationsfel för SCX-paket |
| 51 | Det här tillägget stöds inte på den virtuella datorns driftsystem | |
| 55 | Det går inte att ansluta till Azure Monitor-tjänsten eller nödvändiga paket som saknas eller dpkg-pakethanteraren är låst| Kontrollera att systemet antingen har tillgång till Internet eller att en giltig HTTP-proxy har tillhandahållits. Kontrollera dessutom att arbetsyte-ID:t är korrekt och kontrollera att verktyg för curl och tjära är installerade. |

Ytterligare felsökningsinformation finns i [felsökningsguiden för Log Analytics-Agent-för-Linux](../../azure-monitor/platform/vmext-troubleshoot.md).

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj Hämta support. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
