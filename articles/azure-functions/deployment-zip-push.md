---
title: Zippa push-distribution för Azure Functions | Microsoft Docs
description: Använda .zip-filen distribution anläggningarna i distributionstjänst för Kudu för att publicera dina Azure-funktioner.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
editor: ''
tags: ''
ms.service: functions
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/12/2018
ms.author: glenga
ms.openlocfilehash: 06124a0a0db47d76552ddf2172a7f656c6d869cf
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818475"
---
# <a name="zip-deployment-for-azure-functions"></a>ZIP-distribution för Azure Functions

Den här artikeln beskriver hur du distribuerar dina projektfiler för funktionen app till Azure från en (komprimerad) .zip-fil. Du lär dig hur du gör en push-distribution, både med hjälp av Azure CLI och med hjälp av REST-API: er. [Azure Functions Core Tools](functions-run-local.md) använder även dessa distribution av API: er när du publicerar ett lokalt projekt till Azure.

Azure Functions har en fullständig uppsättning kontinuerlig utveckling och integration alternativ som tillhandahålls av Azure App Service. Mer information finns i [kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md).

Om du vill påskynda utvecklingen, kan det vara lättare att distribuera dina projektfiler för funktionen appen direkt från en .zip-fil. ZIP-distributionen API tar innehållet i en .zip-fil och extraherar innehållet i den `wwwroot` mapp på din funktionsapp. Den här ZIP-filen distributionen använder samma Kudu-tjänsten som driver kontinuerlig integration-baserade distributioner, däribland:

+ Borttagning av filer som var kvar från tidigare distributioner.
+ Distributionsanpassning, inklusive köra distributionsskripten.
+ Distributionsloggar.
+ Synkronisera function-utlösare i ett [förbrukningsplan](functions-scale.md) funktionsapp.

Mer information finns i den [referens för distribution av .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="deployment-zip-file-requirements"></a>Distributionskrav .zip-fil

.Zip-filen som du använder för push-distributionen måste innehålla alla filer som behövs för att köra funktionen.

>[!IMPORTANT]
> När du använder .zip distribution tas filer från en befintlig distribution som inte finns i ZIP-filen bort från funktionsappen.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

En funktionsapp som innehåller alla filer och mappar i den `wwwroot` directory. En .zip-filen distributionen omfattar innehållet i den `wwwroot` directory, men inte den aktuella katalogen. När du distribuerar en C# klassbiblioteksprojektet, måste du inkludera kompilerade library-filer och beroenden i en `bin` undermapp i ZIP-paketet.

## <a name="download-your-function-app-files"></a>Hämta dina funktionen app-filer

När du utvecklar på en lokal dator, är det enkelt att skapa en ZIP-fil i mappen funktionen app-projekt på din utvecklingsdator.

Du kan dock ha skapat dina funktioner med hjälp av redigeraren i Azure-portalen. Du kan hämta en befintlig funktionsappsprojekt i något av följande sätt:

+ **Från Azure-portalen:**

    1. Logga in på den [Azure-portalen](https://portal.azure.com), och gå sedan till din funktionsapp.

    2. På den **översikt** fliken **ladda ned appinnehåll**. Välj alternativ för nedladdning och välj sedan **hämta**.

        ![Ladda ned funktionsapprojekt](./media/deployment-zip-push/download-project.png)

    Hämtade ZIP-filen är i rätt format ompubliceras till din funktionsapp använda .zip push-distribution. Portalen nedladdningen kan också lägga till de filer som behövs för att öppna din funktionsapp direkt i Visual Studio.

+ **Med hjälp av REST API: er:**

    Använd följande distribution hämta API för att hämta filer från din `<function_app>` projekt: 

        https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/

    Inklusive `/site/wwwroot/` gör att zip-filen innehåller bara funktionen app projektfilerna och inte hela platsen. Om du redan inte är inloggad till Azure, blir du ombedd att göra detta.  

Du kan också hämta en ZIP-fil från en GitHub-lagringsplats. När du laddar ned en GitHub-lagringsplats som en .zip-fil lägger GitHub till ett extra mappnivå för grenen. Den här extra mappen på innebär att du inte kan distribuera .zip-filen som du ned det från GitHub. Om du använder en GitHub-lagringsplats för att underhålla din funktionsapp, bör du använda [kontinuerlig integrering](functions-continuous-deployment.md) att distribuera din app.  

## <a name="cli"></a>Distribuera med hjälp av Azure CLI

Du kan använda Azure CLI för att utlösa en push-distribution. Push distribuera en ZIP-fil till din funktionsapp med hjälp av den [az functionapp config-zip-källa distribution](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) kommando. Om du vill använda det här kommandot måste du använda Azure CLI version 2.0.21 eller senare. Om du vill se vilka Azure CLI-version som du använder, Använd den `az --version` kommando.

I följande kommando, ersätter den `<zip_file_path>` med sökvägen till platsen för din ZIP-fil. Dessutom måste du ersätta `<app_name>` med det unika namnet på din funktionsapp. 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```

Det här kommandot distribuerar projektfilerna från hämtade ZIP-filen till din funktionsapp i Azure. Appen startar sedan om. Om du vill visa listan över distributioner för den här funktionsappen, måste du använda REST-API: er.

När du använder Azure CLI på din lokala dator `<zip_file_path>` är sökvägen till .zip-filen på datorn. Du kan också köra Azure CLI i [Azure Cloud Shell](../cloud-shell/overview.md). När du använder Cloud Shell måste du först överföra din distribution ZIP-fil till Azure Files-konto som är associerat med Cloud Shell. I så fall `<zip_file_path>` är den lagringsplats som ditt konto i Cloud Shell använder. Mer information finns i [spara filer i Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Köra funktioner från distributionspaketet

Du kan också välja att köra dina funktioner direkt från paketet distributionsfilen. Den här metoden hoppar över distributionssteg för att kopiera filer från paketet till den `wwwroot` katalog på din funktionsapp. I stället paketfilen monteras med Functions-körning och innehållet i den `wwwroot` directory blir skrivskyddade.  

> [!NOTE]
> Möjligheten att köra din funktionsapp från distributionspaketet genomgår förhandsgranskning.

ZIP-distribution kan integreras med den här funktionen, som du kan aktivera genom att ange funktionsappsinställning `WEBSITE_RUN_FROM_ZIP` till ett värde av `1`. Mer information finns i [kör dina funktioner från en distribution paketfil](run-functions-from-deployment-package.md).

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Löpande distribution för Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
