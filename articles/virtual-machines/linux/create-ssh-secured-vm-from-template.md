---
title: "Skapa en Linux-VM i Azure från en mall | Microsoft Docs"
description: "Hur du använder Azure CLI 2.0 för att skapa en Linux VM från en Resource Manager-mall"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 09/26/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 938304efe5e4a13736a50348bd0531c475149aec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Så här skapar du en virtuell Linux-dator med Azure Resource Manager-mallar
Den här artikeln visar hur du snabbt distribuerar en Linux-dator (VM) med Azure Resource Manager-mallar och Azure CLI 2.0. Du kan också utföra dessa steg med [Azure CLI 1.0](create-ssh-secured-vm-from-template-nodejs.md).


## <a name="templates-overview"></a>Översikt över mallar
Azure Resource Manager-mallarna är JSON-filer som definierar infrastrukturen och konfigurationen av din Azure-lösning. Genom att använda en mall kan du distribuera lösningen flera gånger under dess livscykel och vara säker på att dina resurser distribueras konsekvent. Läs mer om formatet för mallen och hur du skapar i [skapa din första Azure Resource Manager-mallen](../../azure-resource-manager/resource-manager-create-first-template.md). JSON-syntaxen för resurstyper finns i [Define resources in Azure Resource Manager templates](/azure/templates/) (Definiera resurser i Azure Resource Manager-mallar).


## <a name="create-resource-group"></a>Skapa resursgrupp
En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. En resursgrupp måste skapas innan en virtuell dator. I följande exempel skapas en resursgrupp med namnet *myResourceGroupVM* i den *eastus* region:

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Skapa en virtuell dator
I följande exempel skapas en virtuell dator från [Azure Resource Manager-mallen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) med [az distribution skapa](/cli/azure/group/deployment#create). Ange värdet för egna SSH offentlig nyckel, till exempel innehållet i *~/.ssh/id_rsa.pub*. Om du behöver skapa en SSH-nyckel finns [hur du skapar och använder en SSH-nyckel för Linux virtuella datorer i Azure](mac-create-ssh-keys.md).

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
  --parameters '{"sshKeyData": {"value": "ssh-rsa AAAAB3N{snip}B9eIgoZ"}}'
```

I det här exemplet anges en mall som lagras i GitHub. Du kan också hämta eller skapa en mall och ange en lokal sökväg med samma `--template-file` parameter.

Hämta den offentliga IP-adressen till SSH till den virtuella datorn, [az nätverket offentliga ip-visa](/cli/azure/network/public-ip#show):

```azurecli
az network public-ip show \
    --resource-group myResourceGroup \
    --name sshPublicIP \
    --query [ipAddress] \
    --output tsv
```

Du kan sedan SSH till den virtuella datorn som vanligt. Ger dig egen offentlig IP-adress från det föregående kommandot:

```bash
ssh azureuser@<ipAddress>
```

## <a name="next-steps"></a>Nästa steg
I det här exemplet skapas en grundläggande Linux VM. Fler Resource Manager-mallar som inkluderar ramverk för programmet eller skapa mer komplexa miljöer, bläddra i [Azure quickstart mallgalleriet](https://azure.microsoft.com/documentation/templates/).