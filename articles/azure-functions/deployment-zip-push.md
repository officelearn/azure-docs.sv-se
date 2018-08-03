---
title: Zippa push-distribution för Azure Functions | Microsoft Docs
description: Använda .zip-filen distribution anläggningarna i distributionstjänst för Kudu för att publicera dina Azure-funktioner.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/29/2018
ms.author: glenga
ms.openlocfilehash: 3ff02816cdd5641cdcd78a12206b80be6d518373
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423716"
---
# <a name="zip-push-deployment-for-azure-functions"></a>Zippa push-distribution för Azure Functions 
Den här artikeln beskriver hur du distribuerar dina projektfiler för funktionen app till Azure från en (komprimerad) .zip-fil. Du lär dig hur du gör en push-distribution, både med hjälp av Azure CLI och med hjälp av REST-API: er. 

Azure Functions har en fullständig uppsättning kontinuerlig utveckling och integration alternativ som tillhandahålls av Azure App Service. Mer information finns i [kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md). 

För snabbare iteration under utvecklingen är det ofta lättare att distribuera dina projektfiler för funktionen appen direkt från en komprimerad ZIP-fil. Den här ZIP-filen distributionen använder samma Kudu-tjänsten som driver kontinuerlig integration-baserade distributioner, däribland:

+ Borttagning av filer som var kvar från tidigare distributioner.
+ Distributionsanpassning, inklusive köra distributionsskripten.
+ Distributionsloggar.
+ Synkronisera function-utlösare i ett [förbrukningsplan](functions-scale.md) funktionsapp.

Mer information finns i den [referens för distribution av .zip-push](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

## <a name="deployment-zip-file-requirements"></a>Distributionskrav .zip-fil
.Zip-filen som du använder för push-distributionen måste innehålla alla projektfiler i din funktionsapp, inklusive funktionskoden. 

>[!IMPORTANT]
> När du använder push-distribution för .zip tas filer från en befintlig distribution som inte finns i ZIP-filen bort från funktionsappen.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

En funktionsapp som innehåller alla filer och mappar i den `wwwroot` directory. En .zip-filen distributionen omfattar innehållet i den `wwwroot` directory, men inte den aktuella katalogen.  

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

    Inklusive `/site/wwwroot/` gör att zip-filen innehåller bara funktionen app projektfilerna och inte hela platsen. Om du redan inte är inloggad till Azure, blir du ombedd att göra detta. Observera att skicka ett INLÄGG begäran till den `api/zip/` API: et är discoraged av zip distributionsmetod som beskrivs i det här avsnittet. 

Du kan också hämta en ZIP-fil från en GitHub-lagringsplats. Tänk på att när du laddar ned en GitHub-lagringsplats som en .zip-fil GitHub lägger till en extra mappnivå för grenen. Den här extra mappen på innebär att du inte kan distribuera .zip-filen som du ned det från GitHub. Om du använder en GitHub-lagringsplats för att underhålla din funktionsapp, bör du använda [kontinuerlig integrering](functions-continuous-deployment.md) att distribuera din app.  

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

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Löpande distribution för Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
