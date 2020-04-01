---
title: Snabbstart – Skapa en blob med Azure CLI
titleSuffix: Azure Storage
description: I den här snabbstarten får du lära dig hur du använder Azure CLI ladda upp en blob till Azure Storage, ladda ner en blob och lista blobbar i en behållare.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: tamram
ms.openlocfilehash: 2e1b1ac2ea315759b18dc882b98837bca0a84d46
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061446"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Snabbstart: Skapa, hämta och lista blobbar med Azure CLI

Azure CLI är Azures kommandoradsmiljö för att hantera Azure-resurser. Du kan använda det i din webbläsare med Azure Cloud Shell. Du kan även installera det på macOS, Linux eller Windows och köra det från kommandoraden. I den här snabbstarten lär du dig att använda Azure CLI för att ladda upp och ned data till och från Azure Blob Storage.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Krav

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>Installera Azure CLI lokalt

Om du väljer att installera och använda Azure CLI lokalt kräver den här snabbstarten att du kör Azure CLI version 2.0.46 eller senare. Kör `az --version` för att fastställa din version. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

Om du kör Azure CLI lokalt måste du logga in och autentisera. Det här steget är inte nödvändigt om du använder Azure Cloud Shell. Så här loggar du `az login` in på Azure CLI och autentiserar i webbläsarfönstret:

```azurecli
az login
```

Mer information om autentisering med Azure CLI finns [i Logga in med Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="authorize-access-to-blob-storage"></a>Auktorisera åtkomst till Blob-lagring

Du kan auktorisera åtkomst till Blob-lagring från Azure CLI, antingen med Azure AD-autentiseringsuppgifter eller med hjälp av åtkomstnyckeln för lagringskonto. Användning av Azure AD-autentiseringsuppgifter rekommenderas. Den här artikeln visar hur du auktoriserar Blob-lagringsåtgärder med Azure AD.

Azure CLI-kommandon för dataåtgärder mot `--auth-mode` Blob-lagring stöder parametern, vilket gör att du kan ange hur du ska auktorisera en viss åtgärd. Ange `--auth-mode` parametern `login` för att auktorisera med Azure AD-autentiseringsuppgifter. Mer information finns i [Auktorisera åtkomst till blob- eller ködata med Azure CLI](../common/authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Endast Blob storage data `--auth-mode` åtgärder stöder parametern. Hanteringsåtgärder, till exempel skapa en resursgrupp eller ett lagringskonto, använder automatiskt Azure AD-autentiseringsuppgifter för auktorisering.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med kommandot [az group create](/cli/azure/group). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Kom ihåg att ersätta platshållarvärden i vinkelparenteser med dina egna värden:

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Skapa ett allmänt lagringskonto med kommandot [az storage account create](/cli/azure/storage/account). Det allmänna lagringskontot kan användas för alla fyra tjänsterna: blobar, filer, tabeller och köer.

Kom ihåg att ersätta platshållarvärden i vinkelparenteser med dina egna värden:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="create-a-container"></a>Skapa en container

Blobar laddas alltid upp till en container. Du kan ordna grupper av blobbar i behållare som liknar hur du ordnar dina filer på datorn i mappar.

Skapa en behållare för att lagra blobar med kommandot [az storage container create](/cli/azure/storage/container). Kom ihåg att ersätta platshållarvärden i vinkelparenteser med dina egna värden:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

## <a name="upload-a-blob"></a>Ladda upp en blob

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. Exemplen i den här snabbstarten visar hur du arbetar med blockblobar.

Skapa först en fil som ska överföras till en blockblob. Om du använder Azure Cloud Shell använder du följande kommando för att skapa en fil:

```bash
vi helloworld
```

När filen öppnas trycker du på **infoga**. Skriv *Hello world*och tryck sedan på **Esc**. Skriv sedan *:x*och tryck sedan **på Retur**.

I det här exemplet laddar vi upp en blob till den container som vi skapade i det senaste steget med kommandot [az storage blob upload](/cli/azure/storage/blob). Det är inte nödvändigt att ange en filsökväg eftersom filen skapades i rotkatalogen. Kom ihåg att ersätta platshållarvärden i vinkelparenteser med dina egna värden:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file helloworld \
    --auth-mode login
```

Den här åtgärden skapar bloben om den inte redan finns, och skriver över den om den finns. Ladda upp så många filer som du vill innan du fortsätter.

Om du vill ladda upp flera filer samtidigt kan du använda kommandot [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobarna i en container

Lista blobarna i containern med kommandot [az storage blob list](/cli/azure/storage/blob). Kom ihåg att ersätta platshållarvärden i vinkelparenteser med dina egna värden:

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Ladda ned en blob

Använd kommandot [az storage blob download](/cli/azure/storage/blob) för att ladda ned den blob som du laddade upp tidigare. Kom ihåg att ersätta platshållarvärden i vinkelparenteser med dina egna värden:

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>Dataöverföring med AzCopy

Kommandoradsverktyget AzCopy erbjuder högpresterande, skriptbar dataöverföring för Azure Storage. Du kan använda AzCopy för att överföra data till och från Blob-lagring och Azure-filer. Mer information om AzCopy v10, den senaste versionen av AzCopy, finns i [Kom igång med AzCopy](../common/storage-use-azcopy-v10.md). Mer information om hur du använder AzCopy v10 med Blob-lagring finns i [Överföra data med AzCopy- och Blob-lagring](../common/storage-use-azcopy-blobs.md).

I följande exempel används AzCopy för att överföra en lokal fil till en blob. Kom ihåg att ersätta exempelvärdena med dina egna värden:

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort de resurser som du skapade som en del av den här snabbstarten, inklusive lagringskontot, tar du bort resursgruppen med kommandot [az group delete.](/cli/azure/group) Kom ihåg att ersätta platshållarvärden i vinkelparenteser med dina egna värden:

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du överför filer mellan ett lokalt filsystem och en behållare i Azure Blob-lagring. Om du vill veta mer om att arbeta med blobar i Azure Storage kan du fortsätta med de självstudierna.

> [!div class="nextstepaction"]
> [Så gör du: Utföra åtgärder för Blob Storage med Azure CLI](storage-how-to-use-blobs-cli.md)
