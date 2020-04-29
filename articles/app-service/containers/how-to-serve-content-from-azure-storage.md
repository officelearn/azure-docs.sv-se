---
title: Hantera innehåll från Azure Storage till Linux-behållare
description: Lär dig hur du kopplar en anpassad nätverks resurs till din Linux-behållare i Azure App Service. Dela filer mellan appar, hantera statiskt innehåll via fjärr anslutning och komma åt lokalt, osv.
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 79a4e423f7a2b6570234c958ac833cdf5c6a75e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79297925"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Hantera innehåll från Azure Storage i App Service på Linux

> [!NOTE]
> Den här artikeln gäller Linux-behållare. Information om hur du distribuerar till anpassade Windows-behållare finns i [konfigurera Azure Files i en Windows-behållare på App Service](../configure-connect-to-azure-storage.md). Azure Storage i App Service på Linux är en **förhands gransknings** funktion. Den här funktionen **stöds inte i produktions scenarier**.
>

Den här guiden visar hur du kopplar Azure Storage till App Service i Linux. Fördelarna är skyddat innehåll, innehålls portabilitet, beständig lagring, åtkomst till flera appar och flera överförings metoder.

## <a name="prerequisites"></a>Krav

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 eller senare).
- En befintlig [App Service i Linux-appen](https://docs.microsoft.com/azure/app-service/containers/).
- Ett [Azure Storage konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- En [Azure-filresurs och katalog](../../storage/files/storage-how-to-use-files-cli.md).


## <a name="limitations-of-azure-storage-with-app-service"></a>Begränsningar för Azure Storage med App Service

- Azure Storage med App Service är **i för hands version** för App Service på Linux och Web App for containers. Det finns **inte stöd** för **produktions scenarier**.
- Azure Storage med App Service stöder montering **Azure Files behållare** (Läs/skriv) och **Azure Blob-behållare** (skrivskyddat)
- Azure Storage med App Service **stöder inte** användning av konfigurationen av **lagrings brand väggen** på grund av infrastruktur begränsningar.
- Azure Storage med App Service kan du ange **upp till fem** monterings punkter per app.
- Azure Storage som är monterade till en app kan inte nås via App Service FTP/FTPs-slutpunkter. Använd [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
- Azure Storage **ingår inte** i din webbapp och faktureras separat. Läs mer om [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage).

> [!WARNING]
> App Service konfigurationer som använder Azure Blob Storage blir skrivskyddade i feb 2020. [Läs mer](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Konfigurera din app med Azure Storage

När du har skapat ditt [Azure Storage-konto, fil resurs och katalog](#prerequisites)kan du nu konfigurera din app med Azure Storage.

Om du vill montera ett lagrings konto till en katalog i App Service-appen använder [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) du kommandot. Lagrings typen kan vara AzureBlob eller migreringsåtgärden. Migreringsåtgärden används i det här exemplet.


> [!CAUTION]
> Katalogen som anges som monterings Sök väg i din webbapp måste vara tom. Innehåll som lagras i den här katalogen tas bort när en extern montering läggs till. Om du migrerar filer för en befintlig app, gör en säkerhets kopia av din app och dess innehåll innan du börjar.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Du bör göra detta för alla andra kataloger som ska länkas till ett lagrings konto.

## <a name="verify-azure-storage-link-to-the-web-app"></a>Verifiera Azure Storage-länk till webbappen

När en lagrings behållare är länkad till en webbapp kan du kontrol lera detta genom att köra följande kommando:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Använd Azure Storage i Docker Compose

Azure Storage kan monteras med appar för flera behållare med hjälp av anpassade-ID. Om du vill visa namnet på ID för anpassad [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)identitet kör du.

I filen *filen Docker. yml* mappar du `volumes` alternativet till `custom-id`. Ett exempel:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Nästa steg

- [Konfigurera webb program i Azure App Service](../configure-common.md).

