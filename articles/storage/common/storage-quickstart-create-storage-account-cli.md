---
title: "Azure snabbstart – Skapa ett lagringskonto med hjälp av Azure CLI | Microsoft Docs"
description: "Lär dig snabbt att skapa ett nytt lagringskonto med hjälp av Azure CLI."
services: storage
documentationcenter: na
author: tamram
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
ms.author: tamram
ms.openlocfilehash: ed956e3d27d315e0ce4901c2c38d50652f77c09a
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2017
---
# <a name="create-a-storage-account-using-the-azure-cli"></a>Skapa ett lagringskonto med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten beskriver hur du använder Azure CLI för att skapa ett Azure Storage-konto.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med kommandot [az group create](/cli/azure/group#create). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. I det här exemplet skapas en resursgrupp med namnet *myResourceGroup* i regionen *eastus*.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

Om du inte vet vilken region du ska ange för parametern `--location` kan du hämta en lista över regioner som stöds för din prenumeration med kommandot [az account list-locations](/cli/azure/account#list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

## <a name="create-a-general-purpose-storage-account"></a>Skapa ett allmänt lagringskonto

Det finns flera typer av lagringskonton som lämpar sig för olika användningsscenarier, och som var och en har stöd för en eller flera av lagringstjänsterna (Blob Service, File Service, Table Service eller Queue Service). I följande tabell beskrivs de olika typer av lagringskonton som är tillgängliga.

|**Typ av lagringskonto**|**Allmän Standard**|**Allmän Premium**|**Blob Storage, frekvent och lågfrekvent åtkomstnivå**|
|-----|-----|-----|-----|
|**Tjänster som stöds**| Blob Service, File Service, Table Service, Queue Service | Blob Service | Blob Service|
|**Typer av blobbar som stöds**|Blockblobar, sidblobar och tilläggsblobar | Sidblobbar | Blockblobbar och tilläggsblobbar|

Skapa ett allmänt lagringskonto med kommandot [az storage account create](/cli/azure/storage/account#create).

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --kind Storage \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive lagringskontot som du skapade i den här snabbstarten, kan du ta bort resursgruppen med kommandot [az group delete](/cli/azure/group#delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en resursgrupp och ett allmänt standardlagringskonto. Om du vill lära dig hur du överför data till och från lagringskontot kan du fortsätta med snabbstarten om Blob Storage.

> [!div class="nextstepaction"]
> [Överföra objekt till och från Azure Blob Storage med hjälp av Azure CLI](../blobs/storage-quickstart-blobs-cli.md)