---
title: Köra appen från ett ZIP-paket
description: Distribuera appens ZIP-paket med atomicitet. Förbättra förutsägbarheten och tillförlitligheten i appens beteende under ZIP-distributionsprocessen.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 5cc909d79b3f5ea2b4c6a3da12bc7250addbe00c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920730"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Köra din app i Azure App Service direkt från ett ZIP-paket

I [Azure App Service](overview.md)kan du köra dina appar direkt från en ZIP-paketfil för distribution. Den här artikeln visar hur du aktiverar den här funktionen i appen.

Alla andra distributionsmetoder i App Service har något gemensamt: dina filer distribueras till *D:\home\site\wwwroot* i appen (eller */home/site/wwwroot* för Linux-appar). Eftersom samma katalog används av din app vid körning är det möjligt att distributionen misslyckas på grund av fillåskonflikter och att appen fungerar oförutsägbart eftersom vissa av filerna ännu inte har uppdaterats.

När du däremot kör direkt från ett paket kopieras inte filerna i paketet till *katalogen wwwroot.* Istället blir ZIP-paketet självt monterat direkt *wwwroot* som den skrivskyddade wwwroot-katalogen. Det finns flera fördelar med att köra direkt från ett paket:

- Eliminerar fillåskonflikter mellan distribution och körning.
- Säkerställer att endast fullbe distribuerade appar körs när som helst.
- Kan distribueras till en produktionsapp (med omstart).
- Förbättrar prestanda för Azure Resource Manager-distributioner.
- Kan minska kallstartstider, särskilt för JavaScript-funktioner med stora npm-paketträd.

> [!NOTE]
> För närvarande stöds endast ZIP-paketfiler.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Aktivera löpning från paket

Appinställningen `WEBSITE_RUN_FROM_PACKAGE` gör det möjligt att köra från ett paket. Om du vill ange det kör du följande kommando med Azure CLI.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"`kan du köra appen från ett paket lokalt till din app. Du kan också [köra från ett fjärrpaket](#run-from-external-url-instead).

## <a name="run-the-package"></a>Kör paketet

Det enklaste sättet att köra ett paket i din App Service är med Azure CLI [az webapp distribution källa config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) kommando. Ett exempel:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

Eftersom `WEBSITE_RUN_FROM_PACKAGE` appinställningen är inställd extraherar det här kommandot inte paketinnehållet till *appens katalog D:\home\site\wwwroot.* I stället överför den ZIP-filen som den är till *D:\home\data\SitePackages*och skapar ett *packagename.txt* i samma katalog, som innehåller namnet på ZIP-paketet som ska läsas in vid körning. Om du laddar upp ZIP-paketet på ett annat sätt (till exempel [FTP)](deploy-ftp.md)måste du skapa katalogen *D:\home\data\SitePackages* och *packagename.txt-filen* manuellt.

Kommandot startar också om appen. Eftersom `WEBSITE_RUN_FROM_PACKAGE` är inställt monterar App Service det uppladdade paketet som den skrivskyddade *wwwroot-katalogen* och kör appen direkt från den monterade katalogen.

## <a name="run-from-external-url-instead"></a>Kör från extern URL i stället

Du kan också köra ett paket från en extern URL, till exempel Azure Blob Storage. Du kan använda [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) för att överföra paketfiler till ditt Blob-lagringskonto. Du bör använda en privat lagringsbehållare med en [SAS -signatur (Shared Access Signature)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) för att aktivera App Service-körningen för att komma åt paketet på ett säkert sätt. 

När du har laddat upp filen till Blob-lagring och `WEBSITE_RUN_FROM_PACKAGE` har en SAS-URL för filen anger du appinställningen till WEBBADRESSEN. I följande exempel kan du använda Azure CLI:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Om du publicerar ett uppdaterat paket med samma namn på Blob-lagring måste du starta om appen så att det uppdaterade paketet läses in i App Service.

## <a name="troubleshooting"></a>Felsökning

- Att köra direkt `wwwroot` från ett paket gör skrivskyddat. Appen visas ett felmeddelande om den försöker skriva filer till den här katalogen.
- TAR- och GZIP-format stöds inte.
- Den här funktionen är inte kompatibel med [lokal cache](overview-local-cache.md).
- För förbättrad kallstartsprestanda använder du`WEBSITE_RUN_FROM_PACKAGE`det lokala zip-alternativet ( =1).

## <a name="more-resources"></a>Fler resurser

- [Kontinuerlig distribution för Azure App Service](deploy-continuous-deployment.md)
- [Distribuera kod med en ZIP- eller WAR-fil](deploy-zip.md)
