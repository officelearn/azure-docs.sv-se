---
title: "Skapa en Linux VM som använder en Azure-mall med Azure CLI 1.0 | Microsoft Docs"
description: "Skapa en Linux-VM på Azure med hjälp av Azure CLI 1.0 och en Azure Resource Manager-mall."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: v-livech
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33d4aaa78fcdf3bd9e2e236606f2d3049f464a8a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-linux-vm-using-the-azure-cli-10-an-azure-resource-manager-template"></a>Så här skapar du en Linux VM som använder Azure CLI 1.0 en Azure Resource Manager-mall
Den här artikeln visar hur du snabbt distribuerar en Linux-dator med hjälp av Azure CLI 1.0 och en Azure Resource Manager-mall. Artikeln kräver:

* ett Azure-konto ([hämta en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/)).
* den [Azure CLI 1.0](../../cli-install-nodejs.md) loggat in med `azure login`.
* Azure CLI *måste vara i* Azure Resource Manager-läge `azure config mode arm`.

Du kan även snabbt distribuera en virtuell Linux-datormall med hjälp av [Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften
Du kan slutföra uppgiften med någon av följande CLI-versioner:

- [Azure CLI 1.0](#quick-command-summary) – våra CLI för klassisk och resurs management på distributionsmodeller (den här artikeln)
- [Azure CLI 2.0](create-ssh-secured-vm-from-template.md) – vår nästa generations CLI för distributionsmodellen resurshantering

## <a name="quick-command-summary"></a>Snabb kommandosammanfattning
```azurecli
azure group create \
    -n myResourceGroup \
    -l westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>Detaljerad genomgång
Med mallar kan du skapa virtuella datorer i Azure med inställningar som du vill anpassa under starten, till exempel användarnamn och värdnamn. För den här artikeln startar vi en Azure-mall med hjälp av en virtuell Ubuntu-dator tillsammans med en nätverkssäkerhetsgrupp med port 22 öppen för SSH.

Azure Resource Manager-mallar är JSON-filer som kan användas för enkla engångsuppgifter som generering av en virtuell Ubuntu-dator, så som det görs i den här artikeln.  Azure-mallar kan också användas för att skapa komplexa Azure-konfigurationer av hela miljöer som en distributionsstack för testning, utveckling eller produktion.

## <a name="create-the-linux-vm"></a>Skapa den virtuella Linux-datorn
Följande kodexempel illustrerar hur du anropar `azure group create` för att skapa en resursgrupp och distribuera en SSH-skyddad virtuell Linux-dator på samma gång med hjälp av [den här Azure Resource Manager-mallen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Tänk på att i ditt exempel måste du använda namn som är unika för din miljö. Det här exemplet används *myResourceGroup* som resursgruppens namn och *myVM* som namnet på virtuella datorn.

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

Resultatet bör likna följande utdata:

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

I det exemplet distribueras en virtuell dator med parametern `--template-uri`.  Du kan också ladda ned eller skapa en mall lokalt och överföra mallen med hjälp av parametern `--template-file` med en sökväg till mallfilen som argument. Azure CLI uppmanar dig att ange de parametrar som krävs av mallen.

## <a name="next-steps"></a>Nästa steg
Sök i [mallgalleriet](https://azure.microsoft.com/documentation/templates/) för att se vilka appramverk som du ska distribuera härnäst.

