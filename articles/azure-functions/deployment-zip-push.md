---
title: ZIP-push-distribution för Azure Functions | Microsoft Docs
description: Använda ZIP-filen distribution verksamhet för tjänsten Kudu-distribution för att publicera dina Azure-funktioner.
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
ms.openlocfilehash: 91c16ad5a6bf8babffc0b83d801626932688631e
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699962"
---
# <a name="zip-push-deployment-for-azure-functions"></a>ZIP-push-distribution för Azure Functions 
Den här artikeln beskriver hur du distribuerar projektfilerna funktionen app till Azure från en ZIP-fil (komprimerade). Du lär dig att göra en push-distribution med hjälp av Azure CLI såväl med hjälp av REST-API: er. 

Azure Functions har en fullständig uppsättning kontinuerlig distribution och integration alternativ som tillhandahålls av Azure App Service. Mer information finns i [kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md). 

Snabbare upprepas under utvecklingen är det ofta enklare att distribuera projektfilerna funktionen appen direkt från en komprimerad ZIP-fil. Den här ZIP-filen distributionen använder samma Kudu-tjänst som stänger kontinuerlig integration-baserade distributioner, däribland:

+ Borttagning av filer som har kvar från tidigare distributioner.
+ Distributionsanpassning, inklusive skriptkörning för distribution.
+ Distributionsloggar.
+ Synkroniserar funktionen utlösare i en [förbrukning plan](functions-scale.md) funktionsapp.

Mer information finns i [.zip push distribution referens](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

## <a name="deployment-zip-file-requirements"></a>Distributionskrav ZIP-filen
ZIP-filen som du använder för push-distribution måste innehålla alla projektfiler i funktionen appen, inklusive din funktionskoden. 

>[!IMPORTANT]
> När du använder .zip push distribution tas filer från en befintlig distribution som inte finns i ZIP-filen bort från appen funktion.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

En funktionsapp innehåller alla filer och mappar i den `wwwroot` directory. En .zip-filen distributionen omfattar innehållet i den `wwwroot` directory, men inte den aktuella katalogen.  

## <a name="download-your-function-app-files"></a>Hämta din funktionen app-filer

När du utvecklar på en lokal dator är det enkelt att skapa en ZIP-fil i mappen funktionen app-projekt på utvecklingsdatorn. 

Du kan dock har skapat dina funktioner med hjälp av redigeraren i Azure-portalen. Du kan hämta ett befintligt funktionen app-projekt i något av följande sätt: 

+ **Från Azure-portalen:** 

    1. Logga in på den [Azure-portalen](https://portal.azure.com), och gå sedan till din funktionsapp.

    2. På den **översikt** väljer **app hämtningar**. Välj alternativ för hämtning och välj sedan **hämta**.     

        ![Hämta funktionen app-projekt](./media/deployment-zip-push/download-project.png)

    Hämtade ZIP-filen är i rätt format publiceras till funktionen appen med .zip push-distribution. Portalen hämtningen kan också lägga till de filer som behövs för att öppna appen funktionen direkt i Visual Studio.

+ **Med hjälp av REST API: er:** 

    Använd följande distribution hämta API för att hämta filerna från din `<function_app>` projektet: 

        https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/

    Inklusive `/site/wwwroot/` gör att zip-filen innehåller bara de funktion app projektfilerna och inte hela webbplatsen. Om du redan inte är inloggad till Azure, blir du ombedd att göra detta. Observera att skicka en POST-begäran till den `api/zip/` API är discoraged för zip-distributionsmetod som beskrivs i det här avsnittet. 

Du kan också hämta en .zip-fil från en GitHub-databas. Tänk på att när du hämtar en GitHub-databas som en .zip-fil, GitHub lägger till en extra mappnivå för grenen. Den här extra mappen nivå innebär att du inte kan distribuera .zip-filen som du hämtade den från GitHub. Om du använder en GitHub-databas för att underhålla din funktionsapp bör du använda [kontinuerlig integration](functions-continuous-deployment.md) att distribuera din app.  

## <a name="cli"></a>Distribuera med hjälp av Azure CLI

Du kan använda Azure CLI för att utlösa en push-distribution. Push distribuera en .zip-fil i appen funktionen med hjälp av den [az functionapp distribution källa config zip-](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config_zip) kommando. Om du vill använda det här kommandot måste du använda Azure CLI version 2.0.21 eller senare. Om du vill se vilka Azure CLI-versionen som du använder den `az --version` kommando.

I följande kommando och ersätter den `<zip_file_path>` med sökvägen till platsen för ZIP-filen. Dessutom måste du ersätta `<app_name>` med det unika namnet för din funktionsapp. 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```
Det här kommandot distribuerar projektfiler från hämtade ZIP-filen i appen funktionen i Azure. Appen startas sedan om. Om du vill visa listan över distributioner för den här funktionen appen, måste du använda REST-API: er.

När du använder Azure CLI på din lokala dator `<zip_file_path>` är sökvägen till .zip-filen på datorn. Du kan också köra Azure CLI [Azure Cloud Shell](../cloud-shell/overview.md). När du använder molntjänster Shell, måste du först överföra din distribution ZIP-fil på kontot i Azure-filer som är kopplad till molnet gränssnittet. I så fall `<zip_file_path>` är lagringsplatsen som använder molnet Shell-konto. Mer information finns i [spara filer i Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).


[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Löpande distribution för Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
