---
title: Lägg till anpassad lagring (Windows-behållare)
description: Lär dig hur du kopplar en anpassad nätverks resurs i en anpassad Windows-behållare i Azure App Service. Dela filer mellan appar, hantera statiskt innehåll via fjärr anslutning och komma åt lokalt, osv.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "77120682"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Konfigurera Azure Files i en Windows-behållare på App Service

> [!NOTE]
> Den här artikeln gäller anpassade Windows-behållare. Information om hur du distribuerar till App Service på _Linux_finns i [hantera innehåll från Azure Storage](./containers/how-to-serve-content-from-azure-storage.md).
>

Den här guiden visar hur du kommer åt Azure Storage i Windows-behållare. Endast [Azure Files resurser](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) och [Premium fil resurser](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) stöds. Du använder Azure Files resurser i den här instruktionen. Fördelarna är skyddat innehåll, innehålls portabilitet, åtkomst till flera appar och flera överförings metoder.

## <a name="prerequisites"></a>Krav

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 eller senare).
- [En befintlig Windows-container i Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Skapa Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Ladda upp filer till Azure-filresursen](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure Files är icke standard lagring och debiteras separat, inte tillsammans med webbappen. Den stöder inte användning av brand Väggs konfiguration på grund av begränsningar i infrastrukturen.
>

## <a name="limitations"></a>Begränsningar

- Azure Storage i Windows-behållare finns **i för hands version** och **stöds inte** för **produktions scenarier**.
- Azure Storage i Windows-behållare stöder montering **Azure Files behållare** (Läs/skriv).
- Azure Storage i Windows-behållare stöds för närvarande **inte** för att ta med egna kod scenarier i Windows App Service-planer.
- Azure Storage i Windows-behållare **stöder inte** användning av **lagrings brand Väggs** konfigurationen på grund av infrastruktur begränsningar.
- Med Azure Storage i Windows-behållare kan du ange **upp till fem** monterings punkter per app.
- Azure Storage som är monterade till en app kan inte nås via App Service FTP/FTPs-slutpunkter. Använd [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
- Azure Storage faktureras separat och **ingår inte** i din webbapp. Läs mer om [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage).

## <a name="link-storage-to-your-web-app-preview"></a>Länka lagring till din webbapp (förhands granskning)

 Om du vill montera en Azure Files resurs till en katalog i App Service-appen använder du [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) kommandot. Lagrings typen måste vara migreringsåtgärden.

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
