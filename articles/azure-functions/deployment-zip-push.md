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
ms.openlocfilehash: 5c8c608126f20aa5f5dc52bb8e24cfec14fd00f5
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="zip-push-deployment-for-azure-functions"></a>ZIP-push-distribution för Azure Functions 
Det här avsnittet beskriver hur du distribuerar projektfilerna funktionen app till Azure från en ZIP-fil (komprimerade). Du lär dig att göra en push-distribution med både Azure CLI och REST-API: er. Azure Functions har en fullständig uppsättning kontinuerlig distribution och integrationsalternativen i Azure App Service för mer information, se [kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md). Dock snabbare upprepas under utvecklingen är det ofta enklare att distribuera projektfilerna funktionen appen direkt från en komprimerad ZIP-fil.

Den här ZIP-filen distributionen använder samma Kudu-tjänst som stänger kontinuerlig integration-baserade distributioner, som innehåller följande funktioner:

+ Borttagning av filer som kan komma från en tidigare distribution.
+ Distributionsanpassning, inklusive skriptkörning för distribution.
+ Distributionsloggar.
+ Synkronisera funktionen utlösare i en [förbrukning plan](functions-scale.md) funktionsapp.

Mer information finns i [.zip push distribution referensavsnittet]. 

## <a name="requirements-of-the-deployment-zip-file"></a>Krav på distributionen ZIP-filen 
ZIP-filen som du använder för push-distribution måste innehåller alla projektfiler i funktionen appen, inklusive din funktionskoden. 

>[!IMPORTANT]
> När du använder .zip push distribution tas filer från en befintlig distribution hittades inte i ZIP-filen bort från appen funktion.  

### <a name="function-app-folder-structure"></a>Funktionen app-mappstruktur

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

### <a name="downloading-your-function-app-project"></a>Hämta din funktionen app-projekt

När du gör lokal utveckling, är det enkelt att skapa en ZIP-fil i mappen funktionen app-projekt på utvecklingsdatorn. Men har du också skapat dina funktioner med hjälp av redigeraren i portalen. Hämta projektet funktionen appen från portalen: 

1. Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till appen funktion.

2. I den **översikt** väljer **app hämtningar**, Välj Hämtningsalternativ för och på **hämta**.     

    ![Hämta funktionen app-projekt](./media/deployment-zip-push/download-project.png)

Hämtade ZIP-filen är i rätt format publiceras till funktionen appen med .zip push-distribution.

GitHub kan du ladda ned en .zip-fil från en databas. Kom ihåg att när du hämtar en GitHub-databas som en .zip-fil, GitHub lägger till en extra mapp för avdelningskontor, vilket innebär att du inte kan distribuera .zip-filen ned från GitHub är. Om du använder en GitHub-databas för att underhålla din funktionsapp, bör du använda [kontinuerlig integration](functions-continuous-deployment.md) att distribuera din app.  

## <a name="cli"></a>Distribuera med hjälp av Azure CLI

Du kan använda Azure CLI för att utlösa en push-distribution. Push distribuera en .zip-fil i appen funktionen med hjälp av den [az functionapp distribution källa config zip-](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config_zip) kommando. Du måste använda Azure CLI version 2.0.21 eller senare. Använd den `az --version` kommandot för att se vilken version du använder.

I följande kommando och ersätter den `<zip_file_path>` med sökvägen till platsen för ZIP-filen. Dessutom måste du ersätta `<app_name>` med det unika namnet för din funktionsapp. 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```
Det här kommandot distribuerar projektfiler från hämtade ZIP-filen i appen funktionen i Azure och startar om appen. Om du vill visa listan över distributioner för den här funktionen appen, måste du använda REST-API: er.

När du använder Azure CLI på din lokala dator `<zip_file_path>` är sökvägen till .zip-filen på datorn. Du kan också köra Azure CLI den [Azure Cloud Shell](../cloud-shell/overview.md). När du använder molntjänster Shell, måste du först överföra din distribution ZIP-fil till Azure File-konto som är kopplade till molnet-gränssnittet. I så fall `<zip_file_path>` är lagringsplats som används av ditt moln Shell-konto. Mer information finns i [spara filer i Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).


[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Löpande distribution för Azure Functions](functions-continuous-deployment.md)

[.zip push distribution referensavsnittet]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
