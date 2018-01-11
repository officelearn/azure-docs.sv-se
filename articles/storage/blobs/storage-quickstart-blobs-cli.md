---
title: "Snabbstart för Azure – Överföra objekt till och från Azure Blob Storage med hjälp av Azure CLI | Microsoft Docs"
description: "Lär dig snabbt hur du överför objekt till och från Azure Blob Storage med hjälp av Azure CLI"
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
ms.date: 07/19/2017
ms.author: tamram
ms.openlocfilehash: 7313df35baadf7aa6d476f44b113dc60e6845f4b
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-the-azure-cli"></a>Överföra objekt till och från Azure Blob Storage med hjälp av Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. I den här snabbstarten beskriver vi hur du använder Azure CLI för att ladda upp och ned data till och från Azure Blob Storage.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Skapa en behållare

Blobar laddas alltid upp till en behållare. Du kan ordna grupper av blobar på samma sätt som du ordnar filer i mappar på datorn.

Skapa en behållare för att lagra blobar med kommandot [az storage container create](/cli/azure/storage/container#create).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>Ladda upp en blob

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. De flesta filer som lagras i Blob Storage lagras som blockblobar. Tilläggsblobar används när data måste läggas till i en befintlig blob utan att det befintliga innehållet ändras, som vid loggning. Sidblobar säkerhetskopierar VHD-filerna i virtuella IaaS-datorer.

Skapa först en fil som ska överföras till en blob.
Om du använder Azure Cloud Shell använder du följande för att skapa en fil: `vi helloworld` när filen öppnas: tryck på **Insert**, skriv "Hello world" och tryck på **Esc** och skriv `:x` och tryck sedan på **Retur**.

I det här exemplet laddar vi upp en blob till den behållare som vi skapade i det senaste steget med kommandot [az storage blob upload](/cli/azure/storage/blob#upload).

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Om du använde den tidigare beskrivna metoden för att skapa en fil i Azure Cloud Shell kan du använda det här CLI-kommandot i stället (observera att du inte behövde ange en sökväg eftersom filen skapades i baskatalogen, men i vanliga fall hade du behövt ange en sökväg):

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name helloworld
    --file helloworld
```

Den här åtgärden skapar bloben om den inte redan finns, och skriver över den om den finns. Ladda upp så många filer som du vill innan du fortsätter.

Om du vill ladda upp flera filer samtidigt kan du använda kommandot [az storage blob upload-batch](/cli/azure/storage/blob#upload-batch).

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare

Lista blobarna i behållaren med kommandot [az storage blob list](/cli/azure/storage/blob#list).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>Ladda ned en blob

Använd kommandot [az storage blob download](/cli/azure/storage/blob#download) för att ladda ned den blob som du laddade upp tidigare.

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Dataöverföring med AzCopy

Verktyget [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) är ett annat alternativ för högpresterande, skriptbar dataöverföring för Azure Storage. Du kan använda AzCopy för att överföra data till och från Blob, File och Table Storage.

Som ett enkelt exempel ser du här AzCopy-kommandot för att ladda upp en fil med namnet *myfile.txt* till behållaren *mystoragecontainer*.

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://mystorageaccount.blob.core.windows.net/mystoragecontainer \
    --dest-key <storage-account-access-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive lagringskontot som du skapade i den här snabbstarten, kan du ta bort resursgruppen med kommandot [az group delete](/cli/azure/group#delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du överför filer mellan en lokal disk och en behållare i Azure Blob Storage. Om du vill veta mer om att arbeta med blobar i Azure Storage kan du fortsätta med de självstudierna.

> [!div class="nextstepaction"]
> [Så gör du: Utföra åtgärder för Blob Storage med Azure CLI](storage-how-to-use-blobs-cli.md)
