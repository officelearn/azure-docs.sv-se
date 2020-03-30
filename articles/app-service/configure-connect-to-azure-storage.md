---
title: Lägg till anpassad lagring (Windows-behållare)
description: Lär dig hur du bifogar anpassad nätverksresurs i en anpassad Windows-behållare i Azure App Service. Dela filer mellan appar, hantera statiskt innehåll på distans och komma åt lokalt, etc.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120682"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Konfigurera Azure-filer i en Windows-behållare på App-tjänsten

> [!NOTE]
> Den här artikeln gäller anpassade Windows-behållare. Information om hur du distribuerar till App Service på _Linux_finns [i Servera innehåll från Azure Storage](./containers/how-to-serve-content-from-azure-storage.md).
>

Den här guiden visar hur du kommer åt Azure Storage i Windows-behållare. Endast [Azure Files Shares](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) och Premium Files [Shares](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) stöds. Du använder Azure Files Shares i den här how-to. Fördelarna är skyddat innehåll, tillgänglighet för innehåll, åtkomst till flera appar och flera överföringsmetoder.

## <a name="prerequisites"></a>Krav

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 eller senare).
- [En befintlig Windows-behållarapp i Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Skapa Azure-filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Ladda upp filer till Azure File-resurs](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure Files är icke-standardlagring och faktureras separat, ingår inte i webbappen. Det stöder inte användning av brandväggskonfiguration på grund av infrastrukturbegränsningar.
>

## <a name="limitations"></a>Begränsningar

- Azure Storage i Windows-behållare finns **i förhandsversion** och **stöds inte** för **produktionsscenarier**.
- Azure Storage i Windows-behållare stöder montering av **Azure Files-behållare** (läs / skriv) endast.
- Azure Storage i Windows-behållare stöds för närvarande **inte** för att ta med egna kodscenarier i Windows App Service-abonnemang.
- Azure Storage i Windows-behållare **stöder inte** användning av konfigurationen **för lagringsbrandväggen** på grund av infrastrukturbegränsningar.
- Med Azure Storage i Windows-behållare kan du ange **upp till fem** monteringspunkter per app.
- Azure Storage som är monterat på en app är inte tillgängligt via App Service FTP/FTPs-slutpunkter. Använd [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
- Azure Storage faktureras oberoende av oberoende och **ingår inte** i din webbapp. Läs mer om [Azure Storage-priser](https://azure.microsoft.com/pricing/details/storage).

## <a name="link-storage-to-your-web-app-preview"></a>Länka lagring till din webbapp (förhandsgranskning)

 Om du vill montera en Azure-fildelning i en [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) katalog i apptjänstappen använder du kommandot. Lagringstypen måste vara AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Du bör göra detta för alla andra kataloger som du vill ska länkas till en Azure-filresurs.

## <a name="verify"></a>Verifiera

När en Azure Files-resurs är länkad till en webbapp kan du verifiera detta genom att köra följande kommando:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>Nästa steg

- [Migrera en ASP.NET app till Azure App Service med en Windows-behållare (förhandsversion).](app-service-web-tutorial-windows-containers-custom-fonts.md)
