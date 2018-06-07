---
title: OMS Azure tillägg för virtuell dator för Linux | Microsoft Docs
description: Distribuera OMS-agent på Linux-dator som använder ett tillägg för virtuell dator.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/21/2018
ms.author: danis
ms.openlocfilehash: f0d8224e5578a5ae46245e6c70792e962a44c933
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652863"
---
# <a name="oms-virtual-machine-extension-for-linux"></a>OMS tillägg för virtuell dator för Linux

## <a name="overview"></a>Översikt

Log Analytics tillhandahåller funktioner för övervakning, aviseringar, och reparationen i molnet och lokala resurser. Tillägget för virtuell dator OMS-Agent för Linux publiceras och stöds av Microsoft. Tillägget OMS-agent installeras på virtuella Azure-datorer och registrerar virtuella datorer i en befintlig logganalys-arbetsyta. Det här dokumentet beskriver de plattformar som stöds, konfigurationer och distributionsalternativ för OMS-tillägget för virtuell dator för Linux.

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

Tillägget OMS-Agent kan köras mot dessa Linux-distributioner.

| Distribution | Version |
|---|---|
| CentOS Linux | 5, 6 och 7 (x86/x64) |
| Oracle Linux | 5, 6 och 7 (x86/x64) |
| Red Hat Enterprise Linux Server | 5, 6 och 7 (x86/x64) |
| Debian GNU/Linux | 6, 7, 8 och 9 (x86/x64) |
| Ubuntu | 12.04 LTS, 14.04 LTS 16.04 LTS (x86/x64) |
| SUSE Linux Enterprise Server | 11 och 12 (x86/x64) |

### <a name="agent-and-vm-extension-version"></a>Agent och VM-tillägget version
Följande tabell innehåller en mappning av versionen av OMS-VM-tillägget och OMS-Agent paket för varje version. En länk till viktig information om OMS paket agentversionen ingår. Viktig information innehåller information om felkorrigeringar och nya funktioner som är tillgängliga för en viss agent-version.  

| Version för OMS Linux VM-tillägg | OMS-Agent paket version | 
|--------------------------------|--------------------------|
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4-210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3-174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2 125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2-124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center automatiskt etablerar OMS-agenten och ansluter till en standard logganalys-arbetsytan som skapats av ASC i din Azure-prenumeration. Om du använder Azure Security Center kan inte köra stegen i det här dokumentet. Gör detta skriver över den konfigurerade arbetsytan och bryter anslutningen med Azure Security Center.

### <a name="internet-connectivity"></a>Internetanslutning

Tillägget OMS-Agent för Linux kräver att den virtuella måldatorn är ansluten till internet. 

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för tillägget OMS-Agent. Tillägget kräver arbetsyte-ID och arbetsytenyckel från målet logganalys-arbetsytan; Dessa värden kan vara [hittades i logganalys-arbetsytan](../../log-analytics/log-analytics-quick-collect-linux-computer.md#obtain-workspace-id-and-key) i Azure-portalen. Eftersom arbetsytans ska behandlas som känsliga data, bör det lagras i en Inställningskonfiguration för skyddade. Azure för VM-tillägget skyddade inställningsdata krypteras och dekrypteras endast på den virtuella måldatorn. Observera att **workspaceId** och **workspaceKey** är skiftlägeskänsliga.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.6",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

### <a name="property-values"></a>Egenskapsvärden

| Namn | Värdet / exempel |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| Publisher | Microsoft.EnterpriseCloud.Monitoring |
| typ | OmsAgentForLinux |
| typeHandlerVersion | 1.6 |
| workspaceId (t.ex.) | 6f680a37-00c6-41C7-a93f-1437e3462574 |
| workspaceKey (t.ex.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |


## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Mallar är perfekt när du distribuerar en eller flera virtuella datorer som kräver post distributionskonfiguration, till exempel onboarding till logganalys. En Resource Manager-mall som innehåller OMS-agenten VM-tillägget kan hittas på den [Azure Quick Start-galleriet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

JSON-konfiguration för ett tillägg för virtuell dator kan kapslas i den virtuella datorresursen eller placeras i roten eller översta nivån i en Resource Manager JSON-mall. Placeringen av JSON-konfigurationen påverkar värdet av resursens namn och typen. Mer information finns i [ange namn och typ för underordnade resurser](../../azure-resource-manager/resource-manager-templates-resources.md#child-resources). 

I följande exempel förutsätter VM-tillägget är kapslad i den virtuella datorresursen. När kapsla resursen tillägget JSON placeras i den `"resources": []` objekt av den virtuella datorn.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.6",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

När du monterar tillägget JSON i roten på mallen resursnamnet innehåller en referens till den överordnade virtuella datorn och typen visar kapslade konfigurationen.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.6",
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

Azure CLI kan användas för att distribuera OMS-agenten VM-tillägget till en befintlig virtuell dator. Ersätt den *workspaceId* och *workspaceKey* med de från logganalys-arbetsytan. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.6 --protected-settings '{"workspaceKey": "omskey"}' \
  --settings '{"workspaceId": "omsid"}'
```

## <a name="troubleshoot-and-support"></a>Felsöka och stöd

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och genom att använda Azure CLI. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando med hjälp av Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Tillägget utförande-utdatan loggas till följande fil:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Felkoder och deras innebörd

| Felkod | Betydelse | Möjlig åtgärd |
| :---: | --- | --- |
| 9 | Aktivera kallas för tidigt | [Uppdatera Azure Linux-agenten](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) till den senaste tillgängliga versionen. |
| 10 | VM är redan ansluten till logganalys-arbetsytan | Ange stopOnMultipleConnections till false i inställningar för offentliga för att ansluta den virtuella datorn till arbetsytan som angetts i schemat för tillägg, eller ta bort den här egenskapen. Den här virtuella datorn hämtar debiteras när för varje arbetsyta som den är ansluten till. |
| 11 | Ogiltig konfiguration för tillägget | Följ föregående exempel om du vill ange värden för alla krävs för distributionen. |
| 12 | Dpkg package manager är låst | Kontrollera att alla dpkg uppdateringsåtgärder på datorn är klar och försök igen. |
| 17 | Installationsfel för OMS-paket | 
| 19 | OMI paketet installationsfel | 
| 20 | SCX paketet installationsfel |
| 51 | Det här tillägget stöds inte på den Virtuella datorns operativsystem | |
| 55 | Det går inte att ansluta till tjänsten Microsoft Operations Management Suite | Kontrollera att systemet antingen har Internetåtkomst eller att en giltig HTTP-proxy har tillhandahållits. Dessutom kan kontrollera är korrekt arbetsyte-ID. |

Mer information om felsökning finns på den [OMS-Agent för Linux felsökningsguide](../../log-analytics/log-analytics-azure-vmext-troubleshoot.md).

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du lagra en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välja Get support. Information om hur du använder Azure stöder finns i [vanliga frågor om Microsoft Azure-supporten](https://azure.microsoft.com/support/faq/).
