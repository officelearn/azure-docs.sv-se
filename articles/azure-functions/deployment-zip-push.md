---
title: "ZIP-push-distribution för Azure Functions | Microsoft Docs"
description: "Använda ZIP-filen distribution verksamhet för tjänsten Kudu-distribution för att publicera dina Azure-funktioner."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/06/2017
ms.author: glenga
ms.openlocfilehash: faddb73522200f60f18294dc43e8d235943f8bbb
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2017
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

### <a name="function-app-folder-structure"></a>Funktionen app-mappstruktur

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

### <a name="download-your-function-app-project"></a>Ladda ned ditt funktionen app-projekt

När du utvecklar på en lokal dator är det enkelt att skapa en ZIP-fil i mappen funktionen app-projekt på utvecklingsdatorn. 

Du kan dock har skapat dina funktioner med hjälp av redigeraren i Azure-portalen. Hämta projektet funktionen appen från portalen: 

1. Logga in på den [Azure-portalen](https://portal.azure.com), och gå sedan till din funktionsapp.

2. På den **översikt** väljer **app hämtningar**. Välj alternativ för hämtning och välj sedan **hämta**.     

    ![Hämta funktionen app-projekt](./media/deployment-zip-push/download-project.png)

Hämtade ZIP-filen är i rätt format publiceras till funktionen appen med .zip push-distribution.

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
