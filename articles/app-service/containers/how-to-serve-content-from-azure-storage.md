---
title: Leverera innehåll från Azure Storage i App Service i Linux
description: Hur du konfigurerar och leverera innehåll från Azure Storage i Azure App Service i Linux.
author: msangapu
manager: jeconnoc
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 11/01/2018
ms.author: msangapu
ms.openlocfilehash: f28a07d775071a92e6183587add1ceabc48ef416
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968911"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Leverera innehåll från Azure Storage i App Service i Linux

Den här guiden visar hur du hantera statiskt innehåll i App Service i Linux med hjälp av [Azure Storage](/azure/storage/common/storage-introduction). Fördelar skyddat innehåll, innehåll portabilitet, åtkomst till flera appar och flera metoder för överföring. I den här guiden kommer du lära dig hur leverans av innehåll på Azure Storage genom att [konfigurera anpassad lagring](https://blogs.msdn.microsoft.com/appserviceteam/2018/09/24/announcing-bring-your-own-storage-to-app-service/).

## <a name="prerequisites"></a>Förutsättningar

- En befintlig webbapp (App Service på Linux eller Web App for Containers).
- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 eller senare).

## <a name="create-azure-storage"></a>Skapa Azure Storage

> [!NOTE]
> Azure Storage är icke-standard storage och faktureras separat, ingår inte i webbappen.
>

Skapa en Azure [Azure storage-konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli).

```azurecli
#Create Storage Account
az storage account create --name <storage_account_name> --resource-group myResourceGroup

#Create Storage Container
az storage container create --name <storage_container_name> --account-name <storage_account_name>
```

## <a name="upload-files-to-azure-storage"></a>Ladda upp filer till Azure Storage

Om du vill överföra en lokal katalog till storage-kontot du använder den [ `az storage blob upload-batch` ](https://docs.microsoft.com/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload-batch) kommandot som i följande exempel:

```azurecli
az storage blob upload-batch -d <full_path_to_local_directory> --account-name <storage_account_name> --account-key "<access_key>" -s <source_location_name>
```

## <a name="link-storage-to-your-web-app"></a>Lagring av länken till din webbapp

> [!CAUTION]
> Länka en befintlig katalog i en webbapp till ett storage-konto raderas kataloginnehållet. Om du migrerar filer för en befintlig app, göra en säkerhetskopia av din app och dess innehåll innan du börjar.
>

Om du vill montera en storage-konto till en katalog i din App Service-app som du använder den [ `az webapp config storage-account add` ](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) kommando. Lagringstyp kan vara AzureBlob eller AzureFiles. Du kan använda AzureBlob för den här behållaren.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Du bör göra detta för alla andra kataloger som du vill ska kopplas till ett lagringskonto.

## <a name="verify"></a>Verifiera

När en lagringsbehållare är länkad till en webbapp, kan du kontrollera detta genom att köra följande kommando:

```azurecli
az webapp config storage-account list --resource-group <group_name> --name <app_name>
```

## <a name="next-steps"></a>Nästa steg

- [Konfigurera webbappar i Azure App Service](https://docs.microsoft.com/azure/app-service/web-sites-configure).
