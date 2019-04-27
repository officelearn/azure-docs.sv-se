---
title: Chef-tillägg för virtuella Azure-datorer | Microsoft Docs
description: Distribuera Chef-klienten till en virtuell dator med Chef VM-tillägget.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: roiyz
ms.openlocfilehash: 6bd3ea4e664523fe8014be40c51d573ed5158ecf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60800277"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Chef VM-tillägg för Linux och Windows

Chef Software tillhandahåller en DevOps-plattform för automatisering för Linux och Windows som möjliggör hantering av både fysiska och virtuella serverkonfigurationer. VM-tillägget Chef är ett tillägg som möjliggör Chef på virtuella datorer.

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="operating-system"></a>Operativsystem

Chef VM-tillägg stöds av alla de [tillägget stöds operativsystem](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) i Azure.

### <a name="internet-connectivity"></a>Internetanslutning

Chef VM-tillägget kräver att den virtuella måldatorn är ansluten till internet för att hämta Chef klienten nyttolasten från nätverk för innehållsleverans (CDN).  

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för VM-tillägget Chef. Tillägget som kräver minst Chef-Serveradress, verifiering klientnamn och valideringsnyckel för Chef-servern. Dessa värden finns i den `knife.rb` filen i starter-kit.zip som laddas ned när du installerar [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) eller en fristående [Chef Server](https://downloads.chef.io/chef-server). Eftersom valideringsnyckel ska behandlas som känsliga data, ska det konfigureras den **protectedSettings** element, vilket innebär att endast kan dekrypteras på den virtuella måldatorn.

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

### <a name="core-property-values"></a>Egenskapsvärden för kärnor

| Namn | Värdet / exempel | Datatyp
| ---- | ---- | ---- 
| apiVersion | `2017-12-01` | sträng (datum) |
| utgivare | `Chef.Bootstrap.WindowsAzure` | string |
| typ | `LinuxChefClient` (Linux), `ChefClient` (Windows) | string |
| typeHandlerVersion | `1210.12` | sträng (double) |

### <a name="settings"></a>Inställningar

| Namn | Värdet / exempel | Datatyp | Krävs?
| ---- | ---- | ---- | ----
| settings/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | sträng (url) | Y |
| settings/bootstrap_options/validation_client_name | `myorg-validator` | string | Y |
| inställningar/runlist | `recipe[mycookbook::default]` | string | Y |

### <a name="protected-settings"></a>Skyddade inställningarna

| Namn | Exempel | Datatyp | Krävs?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | string | Y |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Mallar kan användas för att distribuera en eller flera virtuella datorer, installera klienten Chef, ansluta till Chef-Server och utför den inledande konfigurationen på servern som definieras av den [kör-list](https://docs.chef.io/run_lists.html)

En Resource Manager-mall som innehåller VM-tillägget Chef kan hittas på den [Azure Quick Start-galleriet](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

JSON-konfiguration för tillägg för virtuell dator kan kapslas i resursen för virtuella datorer eller placeras i roten eller översta nivån i en Resource Manager JSON-mall. Placeringen av JSON-konfigurationen påverkar värdet för resursnamn och typ. Mer information finns i [ange namn och typ för underordnade resurser](../../azure-resource-manager/resource-manager-template-child-resource.md).

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att distribuera Chef VM-tillägg till en befintlig virtuell dator. Ersätt den **validation_key** med innehållet i din nyckel till verifiering (den här filen som en `.pem` tillägg).  Ersätt **validation_client_name**, **chef_server_url** och **run_list** med dessa värden från den `knife.rb` filen i Starter Kit.

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
| 51 | Det här tillägget stöds inte på den Virtuella datorns operativsystem | |

Ytterligare felsökningsinformation finns i den [Chef VM-tillägget readme](https://github.com/chef-partners/azure-chef-extension).

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
