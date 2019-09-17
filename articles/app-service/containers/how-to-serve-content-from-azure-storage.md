---
title: Hantera innehåll från Azure Storage på Linux – App Service
description: Så här konfigurerar och hanterar du innehåll från Azure Storage i Azure App Service i Linux.
author: msangapu
manager: jeconnoc
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 2/04/2019
ms.author: msangapu
ms.openlocfilehash: 97c03ad294bba1f8a0285fff4595991ca0acc8b5
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018263"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Hantera innehåll från Azure Storage i App Service på Linux

Den här guiden visar hur du hanterar statiskt innehåll i App Service på Linux med hjälp av [Azure Storage](/azure/storage/common/storage-introduction). Fördelarna är skyddat innehåll, innehålls portabilitet, beständig lagring, åtkomst till flera appar och flera överförings metoder.

## <a name="prerequisites"></a>Förutsättningar

- En befintlig webbapp (App Service på Linux eller Web App for Containers).
- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 eller senare).

## <a name="create-azure-storage"></a>Skapa Azure Storage

> [!NOTE]
> Azure Storage är icke standard lagring och debiteras separat, inte tillsammans med webbappen.
>
> Att ta med din egen lagring stöder inte användning av konfigurationen av lagrings brand väggen på grund av begränsningar i infrastrukturen.
>

Skapa ett Azure [Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli).

```azurecli
#Create Storage Account
az storage account create --name <storage_account_name> --resource-group myResourceGroup

#Create Storage Container
az storage container create --name <storage_container_name> --account-name <storage_account_name>
```

## <a name="upload-files-to-azure-storage"></a>Ladda upp filer till Azure Storage

Om du vill överföra en lokal katalog till lagrings kontot använder [`az storage blob upload-batch`](https://docs.microsoft.com/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload-batch) du kommandot som i följande exempel:

```azurecli
az storage blob upload-batch -d <full_path_to_local_directory> --account-name <storage_account_name> --account-key "<access_key>" -s <source_location_name>
```

## <a name="link-storage-to-your-web-app-preview"></a>Länka lagring till din webbapp (förhands granskning)

> [!CAUTION]
> Om du länkar en befintlig katalog i en webbapp till ett lagrings konto tas katalog innehållet bort. Om du migrerar filer för en befintlig app, gör en säkerhets kopia av din app och dess innehåll innan du börjar.
>

Om du vill montera ett lagrings konto till en katalog i App Service-appen använder [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) du kommandot. Lagrings typen kan vara AzureBlob eller migreringsåtgärden. Du använder AzureBlob för den här behållaren.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Du bör göra detta för alla andra kataloger som ska länkas till ett lagrings konto.

## <a name="verify"></a>Verifiera

När en lagrings behållare är länkad till en webbapp kan du kontrol lera detta genom att köra följande kommando:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-custom-storage-in-docker-compose"></a>Använd anpassad lagring i Docker Compose

Azure Storage kan monteras med appar för flera behållare med hjälp av anpassade-ID. Om du vill visa namnet på ID för anpassad [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)identitet kör du.

I filen *filen Docker. yml* mappar du `volumes` alternativet till `custom-id`. Exempel:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Nästa steg

- [Konfigurera webb program i Azure App Service](../configure-common.md).
