---
title: Servera innehåll från Azure Storage till Linux-behållare
description: Lär dig hur du bifogar anpassad nätverksresurs till din Linux-behållare i Azure App Service. Dela filer mellan appar, hantera statiskt innehåll på distans och komma åt lokalt, etc.
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 79a4e423f7a2b6570234c958ac833cdf5c6a75e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297925"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Servera innehåll från Azure Storage i App Service på Linux

> [!NOTE]
> Den här artikeln gäller Linux-behållare. Information om hur du distribuerar till anpassade Windows-behållare finns [i Konfigurera Azure-filer i en Windows-behållare på App Service](../configure-connect-to-azure-storage.md). Azure Storage i App Service på Linux är en **förhandsgranskningsfunktion.** Den här funktionen **stöds inte för produktionsscenarier**.
>

Den här guiden visar hur du bifogar Azure Storage till App Service på Linux. Fördelarna är skyddat innehåll, tillgänglighet för innehåll, beständig lagring, åtkomst till flera appar och flera överföringsmetoder.

## <a name="prerequisites"></a>Krav

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 eller senare).
- En befintlig [AppTjänst på Linux-appen](https://docs.microsoft.com/azure/app-service/containers/).
- Ett [Azure-lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- En [Azure-filresurs och katalog](../../storage/files/storage-how-to-use-files-cli.md).


## <a name="limitations-of-azure-storage-with-app-service"></a>Begränsningar för Azure Storage med App Service

- Azure Storage with App Service är **i förhandsversion** för App Service på Linux och Web App for Containers. Det **stöds inte** för **produktionsscenarier**.
- Azure Storage med App Service stöder montering av **Azure Files-behållare** (Läs/Skriv) och **Azure Blob-behållare** (skrivskyddad)
- Azure Storage with App Service **stöder inte** användning av konfigurationen **för lagringsbrandväggen** på grund av infrastrukturbegränsningar.
- Med Azure Storage med App Service kan du ange **upp till fem** monteringspunkter per app.
- Azure Storage som är monterat på en app är inte tillgängligt via App Service FTP/FTPs-slutpunkter. Använd [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
- Azure Storage **ingår inte** i din webbapp och faktureras separat. Läs mer om [Azure Storage-priser](https://azure.microsoft.com/pricing/details/storage).

> [!WARNING]
> App Service-konfigurationer med Azure Blob Storage blir skrivskyddad i februari 2020. [Läs mer](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Konfigurera din app med Azure Storage

När du har skapat ditt [Azure Storage-konto, filresurs och katalog](#prerequisites)kan du nu konfigurera din app med Azure Storage.

Om du vill montera ett lagringskonto i en [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) katalog i apptjänstappen använder du kommandot. Lagringstyp kan vara AzureBlob eller AzureFiles. AzureFiles används i det här exemplet.


> [!CAUTION]
> Katalogen som anges som monteringssökväg i webbappen ska vara tom. Allt innehåll som lagras i den här katalogen tas bort när ett externt fäste läggs till. Om du migrerar filer för en befintlig app gör du en säkerhetskopia av appen och dess innehåll innan du börjar.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Du bör göra detta för alla andra kataloger som du vill ska länkas till ett lagringskonto.

## <a name="verify-azure-storage-link-to-the-web-app"></a>Verifiera Azure Storage-länk till webbappen

När en lagringsbehållare har länkats till en webbapp kan du verifiera detta genom att köra följande kommando:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Använda Azure Storage i Docker Compose

Azure Storage kan monteras med appar med flera behållare med hjälp av det anpassade id:t. Om du vill visa det [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)anpassade id-namnet kör du .

Mappa `volumes` alternativet till i filen *docker-compose.yml* till `custom-id`. Ett exempel:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Nästa steg

- [Konfigurera webbappar i Azure App Service](../configure-common.md).

