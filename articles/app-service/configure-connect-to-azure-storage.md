---
title: Konfigurera lagring med hjälp av Azure Files
description: Så här att konfigurera och ansluta till Azure Files i Windows-behållare på App Service.
author: msangapu-msft
manager: gwallace
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu-msft
ms.openlocfilehash: ea6555e8459b8f372a73d7f943e9a96523d4c791
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789051"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Konfigurera Azure-filer i en Windows-behållare på App Service

> [!NOTE]
> Den här artikeln gäller för anpassade Windows-behållare. Distribuera till App Service på _Linux_, se [leverera innehåll från Azure Storage](./containers/how-to-serve-content-from-azure-storage.md).
>

Den här guiden visar hur du kommer åt Azure Storage i Windows-behållare. Endast [Azure-filresurser](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) och [Premium filresurser](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) stöds. Du kan använda Azure Files-resurser i den här anvisningen. Fördelar skyddat innehåll, innehåll portabilitet, åtkomst till flera appar och flera metoder för överföring.

## <a name="prerequisites"></a>Förutsättningar

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 eller senare).
- [En befintlig app för Windows-behållare i Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Skapa Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Ladda upp filer till Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure Files är icke-standard storage och faktureras separat, ingår inte i webbappen. Det stöder inte med brandväggskonfigurationen på grund av infrastrukturbegränsningar.
>

## <a name="link-storage-to-your-web-app-preview"></a>Lagring av länken till din webbapp (förhandsversion)

 Om du vill montera en Azure Files-resurs till en katalog i din App Service-app som du använder den [ `az webapp config storage-account add` ](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) kommando. Lagringstyp måste vara AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Du bör göra detta för delar av andra kataloger som du vill ska kopplas till en Azure-filer.

## <a name="verify"></a>Verifiera

När en Azure Files-resurs är länkad till en webbapp, kan du kontrollera detta genom att köra följande kommando:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>Nästa steg

- [Migrera en ASP.NET-app till Azure App Service med en Windows-behållare (förhandsversion)](app-service-web-tutorial-windows-containers-custom-fonts.md).