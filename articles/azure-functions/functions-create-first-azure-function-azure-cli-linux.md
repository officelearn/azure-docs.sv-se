---
title: "Skapa din första funktion på Linux från Azure CLI (förhandsversion) | Microsoft Docs"
description: "Lär dig hur du skapar din första Azure-funktion som körs på en standardbild Linux med hjälp av Azure CLI."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.date: 11/07/2017
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 4adc25dbca06271382dc76690c75d3198d59d4be
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="create-your-first-function-running-on-linux-using-the-azure-cli-preview"></a>Skapa din första funktion som körs på Linux med hjälp av Azure CLI (förhandsgranskning)

Azure Functions kan du vara värd för dina funktioner på Linux i Azure App Service standardbehållaren. Den här funktionen är för närvarande under förhandsgranskning. Du kan också [ta med din egen anpassade container](functions-create-function-linux-custom-image.md). 

Det här avsnittet för Snabbstart vägleder dig igenom hur du använder Azure Functions med Azure CLI för att skapa din första funktionsapp på Linux finns i standard Apptjänst behållare. Själva Funktionskoden har distribuerats till avbildningen från en exempel GitHub-databas.    

Följande steg kan användas på en Mac, Windows eller Linux-dator. 

## <a name="prerequisites"></a>Krav 

Följande krävs för att slutföra den här snabbstarten:

+ Ett aktivt [GitHub](https://github.com)-konto. 
+ En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver Azure CLI version 2.0 eller senare i det här avsnittet. Kör `az --version` att hitta den version du har. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Skapa en Linux App Service-plan

Linux-värd för funktioner är för närvarande stöds endast på en App Service-plan. Värd för förbrukning plan stöds inte ännu. Mer information om värd finns [Azure Functions värd planer jämförelse](functions-scale.md). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Skapa en funktionsapp på Linux

Du måste ha en funktionsapp som värd för körningen av dina funktioner på Linux. Funktionen appen är en miljö för körning av funktionskod. Där kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. Skapa en funktionsapp med hjälp av den [az functionapp skapa](/cli/azure/functionapp#create) kommandot med en Linux App Service-plan. 

Ersätt namnet på appen en unik funktion där du ser i följande kommando i `<app_name>` platshållare och lagringskontot namn för `<storage_name>`. `<app_name>` används som DNS-standarddomän för funktionsappen. Därför måste namnet vara unikt bland alla appar i Azure. Den _distribution källadress_ parametern är en exempel-lagringsplatsen i GitHub som innehåller ”Hello World” HTTP aktiveras funktionen.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-source-url https://github.com/Azure-Samples/functions-quickstart-linux
```
När funktionen har app skapats och distribuerats, Azure CLI visar information liknar följande exempel:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

Eftersom `myAppServicePlan` är en Linux-plan inbyggda docker-bild som används för att skapa behållaren som kör funktionen appen på Linux. 

>[!NOTE]  
>Exempel-databasen innehåller för närvarande två skriptfiler [deploy.sh](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/deploy.sh) och [.deployment](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/.deployment). Filen .deployment talar om att använda deploy.sh som distributionsprocessen den [anpassade distributionsskriptet](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script). I den aktuella förhandsversionen krävs skript för att distribuera appen funktionen på en Linux-avbildning.  

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
