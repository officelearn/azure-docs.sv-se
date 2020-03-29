---
title: Chef-tillägg för virtuella Azure-datorer
description: Distribuera chefklienten till en virtuell dator med hjälp av chef-VM-tillägget.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/21/2018
ms.author: akjosh
ms.openlocfilehash: a21b8f2fea7433e9f65fd790321a28ea47a38c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76544726"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Chef VM-tillägg för Linux och Windows

Chef Software tillhandahåller en DevOps-plattform för automatisering för Linux och Windows som möjliggör hantering av både fysiska och virtuella serverkonfigurationer. Chef VM Extension är ett tillägg som aktiverar Chef på virtuella datorer.

## <a name="prerequisites"></a>Krav

### <a name="operating-system"></a>Operativsystem

Chef VM-tillägget stöds på alla [operativsystem för tillägg som stöds](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) i Azure.

### <a name="internet-connectivity"></a>Internetanslutning

Chef VM-tillägget kräver att målvirkad dator är ansluten till internet för att hämta chefklientens nyttolast från cdn (Content Delivery Network).  

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för Chef VM Extension. Tillägget kräver minst chefserver-URL:en, valideringsklientnamnet och valideringsnyckeln för Chef-servern. dessa värden finns i `knife.rb` filen i starter-kit.zip som hämtas när du installerar [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) eller en fristående [Chef Server](https://downloads.chef.io/chef-server). Eftersom valideringsnyckeln ska behandlas som känsliga data bör den konfigureras under elementet **protectedSettings,** vilket innebär att den bara dekrypteras på den virtuella måldatorn.

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
    "typeHandlerVersion": "1210.13",
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

### <a name="core-property-values"></a>Värden för grundläggande egenskaper

| Namn | Värde / Exempel | Datatyp
| ---- | ---- | ----
| apiVersion | `2017-12-01` | sträng (datum) |
| utgivare | `Chef.Bootstrap.WindowsAzure` | sträng |
| typ | `LinuxChefClient`(Linux), `ChefClient` (Windows) | sträng |
| typHandlerVersion | `1210.13` | sträng (dubbel) |

### <a name="settings"></a>Inställningar

| Namn | Värde / Exempel | Datatyp | Krävs?
| ---- | ---- | ---- | ----
| inställningar/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | sträng (url) | Y |
| inställningar/bootstrap_options/validation_client_name | `myorg-validator` | sträng | Y |
| inställningar/körlista | `recipe[mycookbook::default]` | sträng | Y |

### <a name="protected-settings"></a>Skyddade inställningar

| Namn | Exempel | Datatyp | Krävs?
| ---- | ---- | ---- | ---- |
| protectedSetts/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | sträng | Y |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Mallar kan användas för att distribuera en eller flera virtuella datorer, installera chefklienten, ansluta till Chef Server och utföra den första konfigurationen på servern enligt definitionen i [körlistan](https://docs.chef.io/run_lists.html)

En exempelmall för Resource Manager som innehåller chef-VM-tillägget finns i [snabbstartsgalleriet i Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm).

JSON-konfigurationen för ett tillägg för virtuella datorer kan kapslas inuti resursen för den virtuella datorn eller placeras på rot- eller toppnivå i en Resource Manager JSON-mall. Placeringen av JSON-konfigurationen påverkar värdet för resursnamnet och resurstypen. Mer information finns i [Ange namn och typ för underordnade resurser](../../azure-resource-manager/resource-manager-template-child-resource.md).

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att distribuera chef-VM-tillägget till en befintlig virtuell dator. Ersätt **validation_key** med innehållet i valideringsnyckeln (den `.pem` här filen som ett tillägg).  Ersätt **validation_client_name,** **chef_server_url** och **run_list** med dessa värden från `knife.rb` filen i startpaketet.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.13 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>Felsökning och support

Data om tillståndet för tilläggsdistributioner kan hämtas från Azure-portalen och med hjälp av Azure CLI. Om du vill se distributionstillståndet för tillägg för en viss virtuell dator kör du följande kommando med Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

Utdata för tilläggskörning loggas till följande fil:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Felkoder och deras betydelser

| Felkod | Betydelse | Möjliga åtgärder |
| :---: | --- | --- |
| 51 | Det här tillägget stöds inte på den virtuella datorns operativsystem | |

Ytterligare felsökningsinformation finns i [den avläsningstjänst för chef-VM-tillägg](https://github.com/chef-partners/azure-chef-extension).

> [!NOTE]
> För något annat direkt relaterat till Chef, kontakta [Chef Support](https://www.chef.io/support/).

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj Hämta support. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
