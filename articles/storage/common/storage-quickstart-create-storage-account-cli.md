---
title: "Azure Quickstart - skapa ett lagringskonto med hjälp av Azure CLI | Microsoft Docs"
description: "Snabbt lära dig skapa ett nytt lagringskonto med hjälp av Azure CLI."
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/28/2017
ms.author: marsma
ms.openlocfilehash: b1fb2da4acf6e06219d790f2354cada4f1e34285
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-storage-account-using-the-azure-cli"></a>Skapa ett lagringskonto med hjälp av Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstartsguide information med Azure CLI för att skapa ett Azure Storage-konto.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en Azure-resursgrupp med kommandot [az group create](/cli/azure/group#create). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. Det här exemplet skapar en resursgrupp med namnet *myResourceGroup* i den *eastus* region.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

Om du inte vet vilken region som du anger för den `--location` parameter, kan du hämta en lista över regioner som stöds för din prenumeration med den [az konto lista-platser](/cli/azure/account#list) kommando.

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

## <a name="create-a-general-purpose-standard-storage-account"></a>Skapa ett allmänt standardlagringskonto

Det finns flera typer av lagringskonton för olika Användningsscenarier, som har stöd för en eller flera av lagringstjänsterna (blobbar, filer, tabeller eller köer). I följande tabell beskrivs kontotyper tillgängligt lagringsutrymme.

|**Typ av lagringskonto**|**Allmän Standard**|**Allmän Premium**|**Blob Storage, frekvent och lågfrekvent åtkomstnivå**|
|-----|-----|-----|-----|
|**Tjänster som stöds**| BLOB-fil, tabell, Queue-tjänster | Blob Service | Blob Service|
|**Typer av blobbar som stöds**|Blockblobbar, sidblobbar, tilläggsblobbar | Sidblobbar | Blockblobbar och tilläggsblobbar|

Skapa ett allmänt lagringskonto med kommandot [az storage account create](/cli/azure/storage/account#create).

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive storage-konto som du skapade i Snabbstart, ta bort resursgruppen med den [ta bort grupp az](/cli/azure/group#delete) kommando.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapat du en resursgrupp och ett allmänt standardlagringskonto. Information om hur du överför data till och från ditt lagringskonto, fortsätter du till Blob storage Snabbstart.

> [!div class="nextstepaction"]
> [Överför objekt till och från Azure Blob storage med hjälp av Azure CLI](../blobs/storage-quickstart-blobs-cli.md)