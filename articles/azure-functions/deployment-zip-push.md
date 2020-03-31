---
title: Zip push-distribution för Azure-funktioner
description: Använd .zip-fildistributionsfunktionerna i Distributionstjänsten För Kudu för att publicera dina Azure-funktioner.
ms.topic: conceptual
ms.date: 08/12/2018
ms.openlocfilehash: 6bda0859ca4741fe74f572b204e40130c56c46fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769683"
---
# <a name="zip-deployment-for-azure-functions"></a>Zip-distribution för Azure Functions

I den här artikeln beskrivs hur du distribuerar dina funktionsappprojektfiler till Azure från en komprimerad zip-fil.This article describes how to deploy your function app project files to Azure from a .zip (compressed) file. Du lär dig hur du gör en push-distribution, både med hjälp av Azure CLI och genom att använda REST-API:erna. [Azure Functions Core Tools](functions-run-local.md) använder också dessa distributions-API:er när du publicerar ett lokalt projekt till Azure.

Azure Functions har hela skalan av kontinuerliga distributions- och integrationsalternativ som tillhandahålls av Azure App Service. Mer information finns i [Kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md).

För att påskynda utvecklingen kan det vara enklare att distribuera funktionsappprojektfilerna direkt från en ZIP-fil. .zip-distributions-API:et tar innehållet i en ZIP-fil och extraherar innehållet i mappen i funktionsappen. `wwwroot` Den här ZIP-fildistributionen använder samma Kudu-tjänst som driver kontinuerliga integrationsbaserade distributioner, inklusive:

+ Radering av filer som blev över från tidigare distributioner.
+ Anpassning av distribution, inklusive att köra distributionsskript.
+ Distributionsloggar.
+ Synkroniseringsfunktion utlöser i en [funktionsapp för förbrukningsplan.](functions-scale.md)

Mer information finns i [.zip-distributionsreferensen](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="deployment-zip-file-requirements"></a>Krav på distribution av ZIP-zip

Zip-filen som du använder för push-distribution måste innehålla alla filer som behövs för att köra funktionen.

>[!IMPORTANT]
> När du använder ZIP-distribution tas alla filer från en befintlig distribution som inte finns i ZIP-filen bort från funktionsappen.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

En funktionsapp innehåller alla filer och `wwwroot` mappar i katalogen. En ZIP-fildistribution innehåller innehållet `wwwroot` i katalogen, men inte själva katalogen. När du distribuerar ett C#-klassbiblioteksprojekt måste du inkludera kompilerade biblioteksfiler och beroenden i en `bin` undermapp i ZIP-paketet.

## <a name="download-your-function-app-files"></a>Ladda ned dina funktionsappfiler

När du utvecklar på en lokal dator är det enkelt att skapa en ZIP-fil för funktionsappprojektmappen på utvecklingsdatorn.

Du kan dock ha skapat dina funktioner med hjälp av redigeraren i Azure-portalen. Du kan ladda ner ett befintligt funktionsappprojekt på något av följande sätt:

+ **Från Azure-portalen:**

  1. Logga in på [Azure-portalen](https://portal.azure.com)och gå sedan till din funktionsapp.

  2. Välj **Hämta appinnehåll**på fliken **Översikt** . Välj dina nedladdningsalternativ och välj sedan **Hämta**.

      ![Ladda ner funktionsappprojektet](./media/deployment-zip-push/download-project.png)

     Den nedladdade ZIP-filen är i rätt format som ska publiceras på nytt till funktionsappen med hjälp av .zip push-distribution. Hämtningen av portalen kan också lägga till de filer som behövs för att öppna din funktionsapp direkt i Visual Studio.

+ **Använda REST API:er:**

    Använd följande GET-API för distribution `<function_app>` för att hämta filerna från projektet: 

        https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/

    Inklusive `/site/wwwroot/` ser till att zip-filen innehåller endast funktionsappprojektfiler och inte hela webbplatsen. Om du inte redan är inloggad på Azure blir du ombedd att göra det.  

Du kan också hämta en ZIP-fil från en GitHub-databas. När du hämtar en GitHub-databas som en ZIP-fil lägger GitHub till en extra mappnivå för grenen. Den här extra mappnivån innebär att du inte kan distribuera ZIP-filen direkt när du hämtade den från GitHub. Om du använder en GitHub-databas för att underhålla din funktionsapp bör du använda [kontinuerlig integrering](functions-continuous-deployment.md) för att distribuera din app.  

## <a name="deploy-by-using-azure-cli"></a><a name="cli"></a>Distribuera med hjälp av Azure CLI

Du kan använda Azure CLI för att utlösa en push-distribution. Tryck distribuera en ZIP-fil till din funktionsapp med kommandot [az functionapp deployment source config-zip.](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) Om du vill använda det här kommandot måste du använda Azure CLI version 2.0.21 eller senare. Använd kommandot `az --version` om du vill se vilken Azure CLI-version du använder.

I följande kommando ersätter du `<zip_file_path>` platshållaren med sökvägen till platsen för ZIP-filen. Ersätt också `<app_name>` med det unika namnet på `<resource_group>` funktionsappen och ersätt med namnet på resursgruppen.

```azurecli-interactive
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

Det här kommandot distribuerar projektfiler från den nedladdade ZIP-filen till din funktionsapp i Azure. Den startar sedan om appen. Om du vill visa listan över distributioner för den här funktionsappen måste du använda REST-API:erna.

När du använder Azure CLI på `<zip_file_path>` den lokala datorn är sökvägen till ZIP-filen på datorn. Du kan också köra Azure CLI i [Azure Cloud Shell](../cloud-shell/overview.md). När du använder Cloud Shell måste du först ladda upp zip-filen för distributionen till Azure Files-kontot som är associerat med ditt Cloud Shell. I så `<zip_file_path>` fall är lagringsplatsen som ditt Cloud Shell-konto använder. Mer information finns [i Beständiga filer i Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Kör funktioner från distributionspaketet

Du kan också välja att köra dina funktioner direkt från distributionspaketfilen. Den här metoden hoppar över distributionssteget för `wwwroot` att kopiera filer från paketet till katalogen för funktionsappen. Paketfilen monteras i stället av funktionskörningen och `wwwroot` innehållet i katalogen skrivskyddas.  

Zip-distribution integreras med den här funktionen, som `WEBSITE_RUN_FROM_PACKAGE` du kan `1`aktivera genom att ställa in funktionsappinställningen till ett värde av . Mer information finns i [Kör dina funktioner från en distributionspaketfil](run-functions-from-deployment-package.md).

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Löpande distribution för Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
