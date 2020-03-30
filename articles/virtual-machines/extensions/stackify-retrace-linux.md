---
title: Stackify Spåra Azure Linux-agenttillägg
description: Distribuera Stackify Retrace Linux-agenten på en virtuell Linux-dator.
services: virtual-machines-linux
documentationcenter: ''
author: darinhoward
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: akjosh
ms.openlocfilehash: 5914947bd994ee405f253e34c3dd919dd6561898
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253798"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify Retrace Linux Agent Förlängning

## <a name="overview"></a>Översikt

Stackify tillhandahåller produkter som spårar information om ditt program för att snabbt hitta och åtgärda problem. För utvecklarteam är Retrace en helt integrerad, multi-miljö, app prestanda super-power. Den kombinerar flera verktyg varje utvecklingsteam behöver.

Retrace är det enda verktyget som levererar alla följande funktioner i alla miljöer i en enda plattform.

* Hantering av programprestanda (APM)
* Program- och serverloggning
* Felspårning och övervakning
* Server-, program- och anpassade mått

**Om Stackify Linux Agent Extension**

Det här tillägget ger en installationssökväg för Linux-agenten för Retrace. 

## <a name="prerequisites"></a>Krav

### <a name="operating-system"></a>Operativsystem 

Retrace-agenten kan köras mot dessa Linux-distributioner

| Distribution | Version |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS, 16.10 och 17.04 |
| Debian | 7,9+ och 8,2+, 9 |
| Red Hat | 6,7+, 7,1+ |
| CentOS | 6,3+, 7,0+ |

### <a name="internet-connectivity"></a>Internetanslutning

Stackify Agent-tillägget för Linux kräver att målvirtuelldatorn är ansluten till internet. 

Du kan behöva justera nätverkskonfigurationen så att anslutningarna kan staplas, se https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall. 


## <a name="extension-schema"></a>Tilläggsschema

---

Följande JSON visar schemat för tillägget Stackify Retrace Agent. Tillägget kräver `environment` och `activationKey`.

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

## <a name="template-deployment"></a>Malldistribution 

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schemat som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra Tillägget Stackify Retrace Linux Agent under en Azure Resource Manager-malldistribution.  

JSON för en virtuell datortillägg kan kapslas inuti den virtuella datorn resurs, eller placeras på roten eller den översta nivån i en Resource Manager JSON-mall. Placeringen av JSON påverkar värdet för resursnamnet och resurstypen. Mer information finns i Ange namn och typ för underordnade resurser.

I följande exempel förutsätts att Stackify Retrace Linux-tillägget är kapslat i resursen för den virtuella datorn. När tilläggsresursen kapslas placeras JSON i objektet "resurser": [] för den virtuella datorn.

Tillägget kräver `environment` och `activationKey`.

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

När du placerar tillägget JSON i roten av mallen innehåller resursnamnet en referens till den överordnade virtuella datorn och typen återspeglar den kapslade konfigurationen.

```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "<parentVmResource>/StackifyExtension",
        "apiVersion": "[variables('apiVersion')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Stackify.LinuxAgent.Extension",
            "type": "StackifyLinuxAgentExtension",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "environment": "myEnvironment"
            },
            "protectedSettings": {
              "activationKey": "myActivationKey"
            }
        }
    }
```


## <a name="powershell-deployment"></a>PowerShell-distribution

Kommandot `Set-AzVMExtension` kan användas för att distribuera tillägget Stackify Retrace Linux Agent till en befintlig virtuell dator. Innan kommandot körs måste de offentliga och privata konfigurationerna lagras i en PowerShell-hash-tabell.

Tillägget kräver `environment` och `activationKey`.

```powershell
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Azure CLI-distribution 

Azure CLI-verktyget kan användas för att distribuera tillägget Stackify Retrace Linux Agent till en befintlig virtuell dator.  

Tillägget kräver `environment` och `activationKey`.

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="error-codes"></a>Felkoder

| Felkod | Betydelse | Möjliga åtgärder |
| :---: | --- | --- |
| 10 | Installera fel | wget krävs |
| 20 | Installera fel | python krävs |
| 30 | Installera fel | sudo krävs |
| 40 | Installera fel | activationKey krävs |
| 51 | Installera fel | OS-distributioner stöds inte |
| 60 | Installera fel | miljö krävs |
| 70 | Installera fel | Okänt |
| 80 | Aktivera fel | Tjänsten misslyckades |
| 90 | Aktivera fel | Det gick inte att starta tjänsten |
| 100 | Inaktivera fel | Tjänststoppet misslyckades |
| 110 | Inaktivera fel | Det gick inte att ta bort tjänsten |
| 120 | Avinstallationsfel | Tjänststoppet misslyckades |

Om du behöver mer hjälp kan https://support.stackify.comdu kontakta Stackify support på .