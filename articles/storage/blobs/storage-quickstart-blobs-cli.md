---
title: "Azure Quickstart - objekt för överföring till/från Azure Blob storage med hjälp av Azure CLI | Microsoft Docs"
description: "Lär dig snabbt att överföra objekt till och från Azure Blob storage med hjälp av Azure CLI"
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
ms.date: 07/19/2017
ms.author: marsma
ms.openlocfilehash: c9b7e7a1fbc6b67821183ce31bdf2527de490c92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-the-azure-cli"></a>Överför objekt till/från Azure Blob storage med hjälp av Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstartsguide information som använder Azure CLI för att ladda upp och hämta data till och från Azure Blob storage.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Skapa en behållare

Blobbar överförs alltid till en behållare. Behållare kan du ordna grupper med blobbar som du ordna filer i kataloger på datorn.

Skapa en behållare för att lagra blobar med kommandot [az storage container create](/cli/azure/storage/container#create).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>Ladda upp en blobb

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. De flesta filer som lagras i Blob storage lagras som blockblobar. Lägg till blobbar som används när data måste du lägga till en befintlig blob utan att ändra befintlig innehåll, till exempel för loggning. Sidblobar säkerhetskopierar VHD-filerna i virtuella IaaS-datorer.

I det här exemplet laddar vi upp en blobb i behållaren som vi skapade i det sista steget med den [az storage blob överför](/cli/azure/storage/blob#upload) kommando.

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Den här åtgärden skapar bloben om den inte redan finns, och skriver över den om den finns. Ladda upp så många filer som du vill innan du fortsätter.

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare

Lista blobarna i behållaren med kommandot [az storage blob list](/cli/azure/storage/blob#list).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>Ladda ned en blob

Använd den [az storage blob download](/cli/azure/storage/blob#download) för att ladda ned en blob som du överfört tidigare.

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Dataöverföring med AzCopy

Den [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) -verktyget är ett annat alternativ för högpresterande skriptbara dataöverföring för Azure Storage. Du kan använda AzCopy för att överföra data till och från Blob-, fil- och Table storage.

Som ett enkelt exempel här är AzCopy-kommandot för att överföra en fil kallad *minfil.txt* till den *mystoragecontainer* behållare.

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://mystorageaccount.blob.core.windows.net/mystoragecontainer \
    --dest-key <storage-account-access-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive storage-konto som du skapade i Snabbstart, ta bort resursgruppen med den [ta bort grupp az](/cli/azure/group#delete) kommando.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I Snabbstart, du har lärt dig hur du överför filer mellan lokal disk och en behållare i Azure Blob storage. Mer information om hur du arbetar med blobar i Azure Storage finns även i fortsättningen självstudierna för att arbeta med Azure Blob storage.

> [!div class="nextstepaction"]
> [Så här: Blob storage-åtgärder med Azure CLI](storage-how-to-use-blobs-cli.md)
