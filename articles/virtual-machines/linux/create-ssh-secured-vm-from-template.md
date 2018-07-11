---
title: Skapa en Linux-VM i Azure från en mall | Microsoft Docs
description: Hur du använder Azure CLI för att skapa en Linux VM från en Resource Manager-mall
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3e6b431ee55ee73b4f5a69471cca3cc16270198c
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930247"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Hur du skapar en Linux-dator med Azure Resource Manager-mallar
Den här artikeln visar hur du snabbt distribuerar en Linux-dator (VM) med Azure Resource Manager-mallar och Azure CLI. 


## <a name="templates-overview"></a>Översikt över mallar
Azure Resource Manager-mallar är JSON-filer som definierar infrastrukturen och konfigurationen av din Azure-lösning. Genom att använda en mall kan du distribuera lösningen flera gånger under dess livscykel och vara säker på att dina resurser distribueras konsekvent. Mer information om mallformatet och hur du konstruerar finns [skapa din första Azure Resource Manager-mall](../../azure-resource-manager/resource-manager-create-first-template.md). JSON-syntaxen för resurstyper finns i [Define resources in Azure Resource Manager templates](/azure/templates/) (Definiera resurser i Azure Resource Manager-mallar).


## <a name="create-a-resource-group"></a>Skapa en resursgrupp
En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. En resursgrupp måste skapas före den virtuella datorn. I följande exempel skapas en resursgrupp med namnet *myResourceGroupVM* i den *eastus* region:

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
I följande exempel skapas en virtuell dator från [Azure Resource Manager-mallen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) med [az group deployment skapa](/cli/azure/group/deployment#az_group_deployment_create). SSH-autentisering tillåts. När du uppmanas, anger du värdet för en egen offentlig SSH-nyckel, t.ex innehållet i *~/.ssh/id_rsa.pub*. Om du vill skapa ett SSH-nyckelpar kan du läsa [hur du skapar och använder en SSH-nyckelpar för virtuella Linux-datorer i Azure](mac-create-ssh-keys.md).

```azurecli
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

Du har angett en mall som lagras i GitHub i exemplet ovan. Du kan också hämta eller skapa en mall och ange den lokala sökvägen med den `--template-file` parametern.


## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn
Hämta den offentliga IP-adressen med SSH till den virtuella datorn [az vm show](/cli/azure/vm#az-vm-show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name sshvm \
    --show-details \
    --query publicIps \
    --output tsv
```

Du kan sedan SSH till den virtuella datorn som vanligt. Ger dig egna offentliga IP-adress från det föregående kommandot:

```bash
ssh azureuser@<ipAddress>
```

## <a name="next-steps"></a>Nästa steg
I det här exemplet skapade du en grundläggande Linux-VM. Fler Resource Manager-mallar som inkluderar ramverk för programmet eller skapa mer komplexa miljöer, bläddra i [galleriet med snabbstartmallar för Azure](https://azure.microsoft.com/documentation/templates/).