---
title: "OMS Azure tillägg för virtuell dator för Linux | Microsoft Docs"
description: "Distribuera OMS-agent på Linux-dator som använder ett tillägg för virtuell dator."
services: virtual-machines-linux
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7bbf210-7d71-4a37-ba47-9c74567a9ea6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: danis
ms.openlocfilehash: dcb7a777c66200c5046a6ad34dc4ff5d346f13e0
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="oms-virtual-machine-extension-for-linux"></a>OMS tillägg för virtuell dator för Linux

## <a name="overview"></a>Översikt

Operations Management Suite (OMS) ger funktioner för övervakning, aviseringar, och reparationen i molnet och lokala resurser. Tillägget för virtuell dator OMS-Agent för Linux publiceras och stöds av Microsoft. Tillägget OMS-agent installeras på virtuella Azure-datorer och registrerar virtuella datorer i en befintlig OMS-arbetsyta. Det här dokumentet beskriver de plattformar som stöds, konfigurationer och distributionsalternativ för OMS-tillägget för virtuell dator för Linux.

## <a name="prerequisites"></a>Krav

### <a name="operating-system"></a>Operativsystem

Tillägget OMS-Agent kan köras mot dessa Linux-distributioner.

| Distribution | Version |
|---|---|
| CentOS Linux | 5, 6 och 7 |
| Oracle Linux | 5, 6 och 7 |
| Red Hat Enterprise Linux Server | 5, 6 och 7 |
| Debian GNU/Linux | 6, 7 och 8 |
| Ubuntu | 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS |
| SUSE Linux Enterprise Server | 11 och 12 |

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center automatiskt etablerar OMS-agenten och kopplar den till standard log analytics-arbetsyta för Azure-prenumerationen. Om du använder Azure Security Center kan inte köra stegen i det här dokumentet. Gör det skriver över den konfigurerade arbetsytan och bryta länken med Azure Security Center.

### <a name="internet-connectivity"></a>Internetanslutning

Tillägget OMS-Agent för Linux kräver att den virtuella måldatorn är ansluten till internet. 

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för tillägget OMS-Agent. Tillägget kräver arbetsyte-ID och arbetsytenyckel från OMS målarbetsytan; Dessa värden finns i OMS-portalen. Eftersom arbetsytans ska behandlas som känsliga data, bör det lagras i en Inställningskonfiguration för skyddade. Azure för VM-tillägget skyddade inställningsdata krypteras och dekrypteras endast på den virtuella måldatorn. Observera att **workspaceId** och **workspaceKey** är skiftlägeskänsliga.

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
    "typeHandlerVersion": "1.4",
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
| typeHandlerVersion | 1.4 |
| workspaceId (t.ex.) | 6f680a37-00c6-41C7-a93f-1437e3462574 |
| workspaceKey (t.ex.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI + rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ == |


## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Mallar är perfekt när du distribuerar en eller flera virtuella datorer som kräver post distributionskonfiguration, till exempel onboarding till OMS. En Resource Manager-mall som innehåller OMS-agenten VM-tillägget kan hittas på den [Azure Quick Start-galleriet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

JSON-konfiguration för ett tillägg för virtuell dator kan kapslas i den virtuella datorresursen eller placeras i roten eller översta nivån i en Resource Manager JSON-mall. Placeringen av JSON-konfigurationen påverkar värdet av resursens namn och typen. Mer information finns i [ange namn och typ för underordnade resurser](../../azure-resource-manager/resource-manager-template-child-resource.md). 

I följande exempel förutsätter OMS-tillägget är kapslad i den virtuella datorresursen. När kapsla resursen tillägget JSON placeras i den `"resources": []` objekt av den virtuella datorn.

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
    "typeHandlerVersion": "1.4",
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
    "typeHandlerVersion": "1.4",
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

Azure CLI kan användas för att distribuera OMS-agenten VM-tillägget till en befintlig virtuell dator. Ersätt OMS-nyckel och OMS-ID med de från din OMS-arbetsyta. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.4 --protected-settings '{"workspaceKey": "omskey"}' \
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
| 10 | VM är redan ansluten till en OMS-arbetsyta | Ange stopOnMultipleConnections till false i inställningar för offentliga för att ansluta den virtuella datorn till arbetsytan som angetts i schemat för tillägg, eller ta bort den här egenskapen. Den här virtuella datorn hämtar debiteras när för varje arbetsyta som den är ansluten till. |
| 11 | Ogiltig konfiguration för tillägget | Följ föregående exempel om du vill ange värden för alla krävs för distributionen. |
| 12 | Dpkg package manager är låst | Kontrollera att alla dpkg uppdateringsåtgärder på datorn är klar och försök igen. |
| 20 | Aktivera kallas för tidigt | [Uppdatera Azure Linux-agenten](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/update-agent) till den senaste tillgängliga versionen. |
| 51 | Det här tillägget stöds inte på den Virtuella datorns operativsystem | |
| 55 | Det går inte att ansluta till tjänsten Microsoft Operations Management Suite | Kontrollera att systemet antingen har Internetåtkomst eller att en giltig HTTP-proxy har tillhandahållits. Dessutom kan kontrollera är korrekt arbetsyte-ID. |

Mer information om felsökning finns på den [OMS-Agent för Linux felsökningsguide](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#).

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/en-us/support/forums/). Alternativt kan du lagra en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/en-us/support/options/) och välja Get support. Information om hur du använder Azure stöder finns i [vanliga frågor om Microsoft Azure-supporten](https://azure.microsoft.com/en-us/support/faq/).
