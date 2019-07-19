---
title: Konfigurera lagring med hjälp av Azure Files
description: Så här konfigurerar och ansluter du till Azure Files i Windows-behållaren på App Service.
author: msangapu-msft
manager: gwallace
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 2c12bf45c033fea185d976f1e9d644183407b5ac
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297213"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Konfigurera Azure Files i en Windows-behållare på App Service

> [!NOTE]
> Den här artikeln gäller anpassade Windows-behållare. Information om hur du distribuerar till App Service på _Linux_finns i [hantera innehåll från Azure Storage](./containers/how-to-serve-content-from-azure-storage.md).
>

Den här guiden visar hur du kommer åt Azure Storage i Windows-behållare. Endast [Azure Files resurser](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) och [Premium fil resurser](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) stöds. Du använder Azure Files resurser i den här instruktionen. Fördelarna är skyddat innehåll, innehålls portabilitet, åtkomst till flera appar och flera överförings metoder.

## <a name="prerequisites"></a>Förutsättningar

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 eller senare).
- [En befintlig Windows-container i Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Skapa Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Ladda upp filer till Azure-filresursen](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure Files är icke standard lagring och debiteras separat, inte tillsammans med webbappen. Den stöder inte användning av brand Väggs konfiguration på grund av begränsningar i infrastrukturen.
>

## <a name="link-storage-to-your-web-app-preview"></a>Länka lagring till din webbapp (förhands granskning)

 Om du vill montera en Azure Files resurs till en katalog i App Service-appen använder [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) du kommandot. Lagrings typen måste vara migreringsåtgärden.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Du bör göra detta för alla andra kataloger som ska länkas till en Azure Files-resurs.

## <a name="verify"></a>Verifiera

När en Azure Files-resurs är länkad till en webbapp kan du kontrol lera detta genom att köra följande kommando:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>Nästa steg

- [Migrera en ASP.net-app till Azure App Service med hjälp av en Windows-behållare (för hands version)](app-service-web-tutorial-windows-containers-custom-fonts.md).
