---
title: "Skapa en virtuell Linux-dator med hjälp av en Azure-mall | Microsoft Docs"
description: "Skapa en virtuell Linux-dator i Azure med hjälp av en Azure Resource Manager-mall."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 10/24/2016
ms.author: v-livech
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: c8b4c8cd7e6e6e07efbaae7ac024160e1e0a3568
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-create-a-linux-vm-using-an-azure-resourec-manager-template"></a>Så här skapar du en virtuell Linux-dator med hjälp av en Azure Resource Manager-mall
I den här artikeln lär du dig hur du snabbt distribuerar en virtuell Linux-dator i Azure med hjälp av en Azure-mall.  Artikeln kräver:

* ett Azure-konto ([hämta en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/)).
* [Azure CLI](../../cli-install-nodejs.md) inloggad med `azure login`.
* Azure CLI *måste vara i* Azure Resource Manager-läge `azure config mode arm`.

Du kan även snabbt distribuera en virtuell Linux-datormall med hjälp av [Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

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
Följande kodexempel illustrerar hur du anropar `azure group create` för att skapa en resursgrupp och distribuera en SSH-skyddad virtuell Linux-dator på samma gång med hjälp av [den här Azure Resource Manager-mallen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Tänk på att i ditt exempel måste du använda namn som är unika för din miljö. I det här exemplet används `myResourceGroup` som resursgruppens namn, `myVM` och som namn på den virtuella datorn.

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


