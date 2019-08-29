---
title: Chefs tillägg för virtuella Azure-datorer | Microsoft Docs
description: Distribuera chefs klienten till en virtuell dator med hjälp av VM-tillägget kock.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/21/2018
ms.author: roiyz
ms.openlocfilehash: 0ed042b3b004fd43bc03304c3042c2ac3e1a6482
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092558"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Kock VM-tillägg för Linux och Windows

Chef Software tillhandahåller en DevOps-plattform för automatisering för Linux och Windows som möjliggör hantering av både fysiska och virtuella serverkonfigurationer. Kock VM-tillägget är ett tillägg som möjliggör chef på virtuella datorer.

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

VM-tillägget för chef stöds för alla [tillägg som stöds av operativ systemet](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) i Azure.

### <a name="internet-connectivity"></a>Internetanslutning

VM-tillägget för chef kräver att den virtuella mål datorn är ansluten till Internet för att hämta chefs klientens nytto Last från Content Delivery Network (CDN).  

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för VM-tillägget för chef. Tillägget kräver minst chefs serverns URL, verifierings klientens namn och validerings nyckeln för chefs servern. dessa värden finns i `knife.rb` filen Starter-Kit. zip som hämtas när du installerar chef för [Automatisk](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) eller fristående chefs [Server](https://downloads.chef.io/chef-server). Eftersom validerings nyckeln ska behandlas som känsliga data, bör den konfigureras under **protectedSettings** -elementet, vilket innebär att den endast dekrypteras på den virtuella mål datorn.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'),'/', parameters('chef_vm_extension_type'))]",
  "apiVersion": "2017-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Chef.Bootstrap.WindowsAzure",
    "type": "[parameters('chef_vm_extension_type')]",
    "typeHandlerVersion": "1210.12",
    "settings": {
      "bootstrap_options": {
        "chef_server_url": "[parameters('chef_server_url')]",
        "validation_client_name": "[parameters('chef_validation_client_name')]"
      },
      "runlist": "[parameters('chef_runlist')]"
    },
    "protectedSettings": {
      "validation_key": "[parameters('chef_validation_key')]"
    }
  }
}  
```

### <a name="core-property-values"></a>Egenskaps värden för kärna

| Name | Värdet / exempel | Datatyp
| ---- | ---- | ----
| apiVersion | `2017-12-01` | string (date) |
| publisher | `Chef.Bootstrap.WindowsAzure` | sträng |
| type | `LinuxChefClient`(Linux), `ChefClient` (Windows) | sträng |
| typeHandlerVersion | `1210.12` | string (double) |

### <a name="settings"></a>Inställningar

| Name | Värdet / exempel | Datatyp | Krävs?
| ---- | ---- | ---- | ----
| settings/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | string (url) | J |
| Settings/bootstrap_options/validation_client_name | `myorg-validator` | sträng | J |
| settings/runlist | `recipe[mycookbook::default]` | sträng | J |

### <a name="protected-settings"></a>Skyddade inställningar

| Name | Exempel | Datatyp | Krävs?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | sträng | J |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Mallar kan användas för att distribuera en eller flera virtuella datorer, installera chefs klienten, ansluta till chefs servern och utföra den inledande konfigurationen på servern som definieras i [körnings listen](https://docs.chef.io/run_lists.html)

En exempel Resource Manager-mall som innehåller det virtuella chefs tillägget för VM finns i [Azure snabb starts galleriet](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

JSON-konfiguration för tillägg för virtuell dator kan kapslas i resursen för virtuella datorer eller placeras i roten eller översta nivån i en Resource Manager JSON-mall. Placeringen av JSON-konfigurationen påverkar värdet för resursnamn och typ. Mer information finns i [ange namn och typ för underordnade resurser](../../azure-resource-manager/resource-manager-template-child-resource.md).

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att distribuera kock VM-tillägget till en befintlig virtuell dator. Ersätt **validation_key** med innehållet i validerings nyckeln (filen som ett `.pem` tillägg).  Ersätt **validation_client_name**, **chef_server_url** och **run_list** `knife.rb` med dessa värden från filen i ditt Start paket.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.12 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>Felsökning och support

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och med hjälp av Azure CLI. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando med hjälp av Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

Tillägget utförande-utdatan loggas till följande fil:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Felkoder och deras innebörd

| Felkod | Betydelse | Möjlig åtgärd |
| :---: | --- | --- |
| 51 | Det här tillägget stöds inte på den virtuella datorns operativ system | |

Ytterligare felsöknings information finns i [filen chef VM Extension](https://github.com/chef-partners/azure-chef-extension).

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
