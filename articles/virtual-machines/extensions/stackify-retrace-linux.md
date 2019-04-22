---
title: Stackify gå Azure Linux Agent-tillägget | Microsoft Docs
description: Distribuera Stackify gå Linux-agenten på en Linux-dator.
services: virtual-machines-linux
documentationcenter: ''
author: darinhoward
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: roiyz
ms.openlocfilehash: b9c035c1c9088957f59550bf6564cc02bc7972f4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58792430"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify gå tillägget för Linux-Agent

## <a name="overview"></a>Översikt

Stackify tillhandahåller produkter som spårar information om ditt program för att hitta och åtgärda problem snabbt. För utvecklare grupper är gå helt integrerad, miljöer, app prestanda överordnad upphöjt. Den kombinerar flera verktyg måste varje Utvecklingsteamet.

Gå är det endast som innehåller alla av följande funktioner över alla miljöer i en enda plattform.

* Hantering av programprestanda (APM)
* Program- och server-loggning
* Fel vid spårning och övervakning
* Server, program och anpassade mått

**Om Stackify Linux-Agenttillägg**

Det här tillägget tillhandahåller en installationssökvägen för Linux-agenten för gå. 

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="operating-system"></a>Operativsystem 

Bläddra-agenten kan köras mot dessa Linux-distributioner

| Distribution | Version |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS, 16,10 och nr 17.04 från |
| Debian | 7,9 + och 8.2 +, 9 |
| Red Hat | 6.7+, 7.1+ |
| CentOS | 6.3+, 7.0+ |

### <a name="internet-connectivity"></a>Internetanslutning

Stackify-agenttillägg för Linux kräver att den virtuella måldatorn är ansluten till internet. 

Du kan behöva justera din nätverkskonfiguration för att tillåta anslutningar till Stackify, se https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall. 


## <a name="extension-schema"></a>Tilläggsschema

---

Följande JSON visar schemat för Stackify gå Agent-tillägget. Tillägget kräver den `environment` och `activationKey`.

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

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schemat som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra tillägget Stackify gå Linux-Agent under en malldistribution för Azure Resource Manager.  

JSON för tillägg för virtuell dator kan kapslas i resursen för virtuella datorer eller placeras i roten eller översta nivån i en Resource Manager JSON-mall. Placeringen av JSON påverkar värdet för resursnamn och typ. Mer information finns i namn och typ för underordnade resurser.

I följande exempel förutsätter Stackify gå Linux-tillägget är kapslade i den virtuella datorresursen. När kapsla tillägget resursen JSON placeras i ”resurser”: [] objekt av den virtuella datorn.

Tillägget kräver den `environment` och `activationKey`.

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

När du monterar tillägget JSON i roten på mallen resursnamnet innehåller en referens till den överordnade virtuella datorn och typen återspeglar den kapslade konfigurationen.

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

Den `Set-AzVMExtension` kommando kan användas för att distribuera Stackify gå Linux-Agent-tillägget för virtuell dator till en befintlig virtuell dator. Innan du kör kommandot, måste de offentliga och privata konfigurationerna lagras i en PowerShell-hash-tabell.

Tillägget kräver den `environment` och `activationKey`.

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

Verktyget Azure CLI kan användas för att distribuera Stackify gå Linux-Agent-tillägget för virtuell dator till en befintlig virtuell dator.  

Tillägget kräver den `environment` och `activationKey`.

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="error-codes"></a>Felkoder

| Felkod | Betydelse | Möjlig åtgärd |
| :---: | --- | --- |
| 10 | Installationsfel | wget krävs |
| 20 | Installationsfel | Python krävs |
| 30 | Installationsfel | sudo krävs |
| 40 | Installationsfel | activationKey krävs |
| 51 | Installationsfel | OS-distribution som inte stöds |
| 60 | Installationsfel | miljö krävs |
| 70 | Installationsfel | Inte tillgängligt |
| 80 | Aktivera fel | Installation av Service misslyckades |
| 90 | Aktivera fel | Tjänsten kunde inte startas |
| 100 | Inaktivera det när | Tjänststoppet misslyckades |
| 110 | Inaktivera det när | Det gick inte att ta bort tjänsten |
| 120 | Fel vid avinstallation | Tjänststoppet misslyckades |

Om du behöver mer hjälp kan du kontakta Stackify support https://support.stackify.com.