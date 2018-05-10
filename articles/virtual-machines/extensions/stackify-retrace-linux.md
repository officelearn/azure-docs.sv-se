---
title: Stackify gå Azure Linux-agenten tillägget | Microsoft Docs
description: Distribuera Stackify gå Linux-agenten på en virtuell Linux-dator.
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
ms.author: danis
ms.openlocfilehash: 376c5a087f74fbe087db9fa2df38b2ba4e6cf1ff
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify gå tillägg för Linux-Agent

## <a name="overview"></a>Översikt
Stackify tillhandahåller produkter som spårar information om ditt program för att hitta och åtgärda problem snabbt. För utvecklare team är gå helt integrerade, flera miljö app prestanda överordnad upphöjt. Det kombinerar flera verktyg måste varje Utvecklingsteamet.

Gå är endast ett verktyg som ger alla följande funktioner i alla miljöer i en enda plattform.

* Programhantering prestanda (APM)
* Program- och server-loggning
* Fel-spårning och övervakning
* Server, program och anpassade mått

**Om Stackify tillägg Linux-Agent**

Det här tillägget tillhandahåller en installationssökväg för Linux-Agent för Bläddra. 

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem 
Bläddra-agenten kan köras mot dessa Linux-distributioner

| Distribution | Version |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS, 16,10 och nr 17.04 från |
| Debian | 7,9 + och 8.2 +, 9 |
| Redhat | 6.7 +, 7.1 + |
| CentOS | 6.3 +, 7.0 + |

### <a name="internet-connectivity"></a>Internetanslutning
Tillägget Stackify Agent för Linux kräver att den virtuella måldatorn är ansluten till internet. 

Du kan behöva justera nätverkskonfigurationen för att tillåta anslutningar till Stackify finns https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall. 


## <a name="extension-schema"></a>Tilläggsschema
---

Följande JSON visar schemat för tillägget Stackify gå Agent. Tillägget kräver den `environment` och `activationKey`.

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

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schema som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra tillägget Stackify gå Linux-agenten under en Azure Resource Manager för malldistribution.  

JSON för ett tillägg för virtuell dator kan kapslas i den virtuella datorresursen eller placeras i roten eller översta nivån i en Resource Manager JSON-mall. Placeringen av JSON påverkar värdet av resursens namn och typen. Mer information finns i Ange namn och typ för underordnade resurser.

I följande exempel förutsätter Stackify gå Linux-tillägget är kapslad i den virtuella datorresursen. När kapsla resursen tillägget JSON placeras i ”resurser”: [] objekt av den virtuella datorn.

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

När du monterar tillägget JSON i roten på mallen resursnamnet innehåller en referens till den överordnade virtuella datorn och typen visar kapslade konfigurationen.

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

Den `Set-AzureRmVMExtension` kommando kan användas för att distribuera Stackify gå Linux-agenten tillägget för virtuell dator till en befintlig virtuell dator. Innan du kör kommandot måste de offentliga och privata konfigurationerna lagras i en PowerShell-hash-tabell.

Tillägget kräver den `environment` och `activationKey`.

```
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzureRmVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
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

Azure CLI-verktyg kan användas för att distribuera Stackify gå Linux-agenten tillägget för virtuell dator till en befintlig virtuell dator.  

Tillägget kräver den `environment` och `activationKey`.

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Felsöka och stöd

### <a name="error-codes"></a>Felkoder

| Felkod | Betydelse | Möjlig åtgärd |
| :---: | --- | --- |
| 10 | Installationsfel | wget krävs |
| 20 | Installationsfel | Python krävs |
| 30 | Installationsfel | sudo krävs |
| 40 | Installationsfel | activationKey krävs |
| 51 | Installationsfel | OS-distro stöds inte |
| 60 | Installationsfel | miljö krävs |
| 70 | Installationsfel | Okänt |
| 80 | Aktivera fel | Installation av Service misslyckades |
| 90 | Aktivera fel | Tjänststarten misslyckades |
| 100 | Inaktivera fel | Tjänsten stoppas misslyckades |
| 110 | Inaktivera fel | Det gick inte att ta bort Service |
| 120 | Fel vid avinstallation | Tjänsten stoppas misslyckades |

Om du behöver mer hjälp kan du kontakta Stackify support på https://support.stackify.com.