---
title: Azure snabbstart – Skapa en blob i objektlagring med hjälp av Azure CLI | Microsoft Docs
description: I den här snabbstarten använder du Azure CLI i objektlagring (Blob). Sedan använder du CLI för att ladda upp en blob till Azure Storage, ladda ned en blob och lista blobarna i en container.
services: storage
author: roygara
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: rogarana
ms.openlocfilehash: 106c84a6eaff3766c5308ccb130dc7c21e7ec4d8
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397020"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-cli"></a>Snabbstart: Ladda upp, ladda ned och lista blobar med Azure CLI

Azure CLI är Azures kommandoradsmiljö för att hantera Azure-resurser. Du kan använda det i din webbläsare med Azure Cloud Shell. Du kan även installera det på macOS, Linux eller Windows och köra det från kommandoraden. I den här snabbstarten lär du dig att använda Azure CLI för att ladda upp och ned data till och från Azure Blob Storage.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att fastställa din version. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Skapa en container

Blobar laddas alltid upp till en container. Du kan ordna grupper av blobar på samma sätt som du ordnar filer i mappar på datorn.

Skapa en behållare för att lagra blobar med kommandot [az storage container create](/cli/azure/storage/container#az_storage_container_create).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>Ladda upp en blob

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. De flesta filer som lagras i Blob Storage lagras som blockblobar. Tilläggsblobar används när data måste läggas till i en befintlig blob utan att det befintliga innehållet ändras, som vid loggning. Sidblobar säkerhetskopierar VHD-filerna i virtuella IaaS-datorer.

Skapa först en fil som ska överföras till en blob.
Om du använder Azure Cloud Shell använder du följande för att skapa en fil: `vi helloworld` när filen öppnas: tryck på **Insert**, skriv "Hello world" och tryck på **Esc** och skriv `:x` och tryck sedan på **Retur**.

I det här exemplet laddar vi upp en blob till den container som vi skapade i det senaste steget med kommandot [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload).

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

Om du vill ladda upp flera filer samtidigt kan du använda kommandot [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch).

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobarna i en container

Lista blobarna i containern med kommandot [az storage blob list](/cli/azure/storage/blob#az_storage_blob_list).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>Ladda ned en blob

Använd kommandot [az storage blob download](/cli/azure/storage/blob#az_storage_blob_download) för att ladda ned den blob som du laddade upp tidigare.

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

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive lagringskontot som du skapade i den här snabbstarten, kan du ta bort resursgruppen med kommandot [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du överför filer mellan en lokal disk och en container i Azure Blob Storage. Om du vill veta mer om att arbeta med blobar i Azure Storage kan du fortsätta med de självstudierna.

> [!div class="nextstepaction"]
> [Så gör du: Utföra åtgärder för Blob Storage med Azure CLI](storage-how-to-use-blobs-cli.md)
