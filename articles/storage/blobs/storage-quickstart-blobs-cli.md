---
title: Azure snabbstart – Skapa en blob i objektlagring med hjälp av Azure CLI | Microsoft Docs
description: I den här snabb starten får du lära dig hur du använder Azure CLI för att ladda upp en blob till Azure Storage, laddar ned en blob och listar blobarna i en behållare.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 1f3143eced90f97c090c0005375ef50fe48c5f5f
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747943"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-cli"></a>Snabbstart: Ladda upp, ladda ned och lista blobar med Azure CLI

Azure CLI är Azures kommandoradsmiljö för att hantera Azure-resurser. Du kan använda det i din webbläsare med Azure Cloud Shell. Du kan även installera det på macOS, Linux eller Windows och köra det från kommandoraden. I den här snabbstarten lär du dig att använda Azure CLI för att ladda upp och ned data till och från Azure Blob Storage.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att fastställa din version. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Skapa en container

Blobar laddas alltid upp till en container. Du kan ordna grupper av blobar på samma sätt som du ordnar filer i mappar på datorn.

Skapa en behållare för att lagra blobar med kommandot [az storage container create](/cli/azure/storage/container).

```azurecli-interactive
az storage container create --name sample-container
```

## <a name="upload-a-blob"></a>Ladda upp en blob

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. I exemplen i den här snabb starten visas hur du arbetar med block-blobbar.

Börja med att skapa en fil som ska laddas upp till en Block-Blob. Om du använder Azure Cloud Shell använder du följande kommando för att skapa en fil:

```bash
vi helloworld
```

När filen öppnas trycker du på **Infoga**. Skriv *Hej världen*och tryck sedan på **ESC**. Skriv sedan *: x*och tryck på **RETUR**.

I det här exemplet laddar vi upp en blob till den container som vi skapade i det senaste steget med kommandot [az storage blob upload](/cli/azure/storage/blob). Du behöver inte ange en fil Sök väg sedan filen skapades i rot katalogen:

```azurecli-interactive
az storage blob upload \
    --container-name sample-container \
    --name helloworld \
    --file helloworld
```

Den här åtgärden skapar bloben om den inte redan finns, och skriver över den om den finns. Ladda upp så många filer som du vill innan du fortsätter.

Om du vill ladda upp flera filer samtidigt kan du använda kommandot [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobarna i en container

Lista blobarna i containern med kommandot [az storage blob list](/cli/azure/storage/blob).

```azurecli-interactive
az storage blob list \
    --container-name sample-container \
    --output table
```

## <a name="download-a-blob"></a>Ladda ned en blob

Använd kommandot [az storage blob download](/cli/azure/storage/blob) för att ladda ned den blob som du laddade upp tidigare.

```azurecli-interactive
az storage blob download \
    --container-name sample-container \
    --name helloworld \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Dataöverföring med AzCopy

Verktyget [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) är ett annat alternativ för högpresterande, skriptbar dataöverföring för Azure Storage. Du kan använda AzCopy för att överföra data till och från Blob, File och Table Storage.

I följande exempel används AzCopy för att ladda upp en fil med namnet *File. txt* till behållaren för *exempel behållare* . Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://<account-name>.blob.core.windows.net/sample-container \
    --dest-key <account-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resurs gruppen, inklusive lagrings kontot som du skapade i den här snabb starten, tar du bort resurs gruppen med kommandot [AZ Group Delete](/cli/azure/group) . Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```azurecli-interactive
az group delete --name <resource-group-name>
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du överför filer mellan ett lokalt fil system och en behållare i Azure Blob Storage. Om du vill veta mer om att arbeta med blobar i Azure Storage kan du fortsätta med de självstudierna.

> [!div class="nextstepaction"]
> [Så gör du: Utföra åtgärder för Blob Storage med Azure CLI](storage-how-to-use-blobs-cli.md)
