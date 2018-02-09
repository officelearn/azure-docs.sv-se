---
title: "Skapa din första funktion i Linux med Azure CLI (förhandsversion) | Microsoft Docs"
description: "Lär dig hur du skapar din första Azure-funktion som körs på en Linux-standardavbildning med Azure CLI."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 49931155339660fc7a0a39f5b60dc9443374b8b0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="create-your-first-function-running-on-linux-using-the-azure-cli-preview"></a>Skapa din första funktion som körs på Linux med hjälp av Azure CLI (förhandsversion)

Med hjälp av Azure Functions kan Linux användas som värd för funktionerna i en Azure App Service-standardbehållare. Du kan även [använda en egen anpassad behållare](functions-create-function-linux-custom-image.md). Den här funktionen ges för närvarande i förhandsversion och kräver [Functions 2.0 runtime](functions-versions.md), som också finns som förhandsversion.

Det här snabbstartsavsnittet visar dig hur du kan använda Azure Functions med Azure CLI för att skapa den första appen i Linux som ligger i App Service-standardbehållaren. Själva funktionskoden distribueras till avbildningen från en GitHub-exempellagringsplats.    

Följande steg kan användas på en Mac-, Windows- eller Linux-dator. 

## <a name="prerequisites"></a>Nödvändiga komponenter 

Följande krävs för att slutföra den här snabbstarten:

+ En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du använda Azure CLI version 2.0.21 eller senare. Kör `az --version` för att se vilken version du har. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Skapa en Linux App Service-plan

Linux-värd för funktioner stöds för närvarande endast i en App Service-plan. Värd för förbrukningsplan stöds inte än. Mer information om värdfunktioner finns i [Azure Functions hosting plans comparison](functions-scale.md) (Jämförelse av Azure Functions-värdplaner). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Skapa en funktionsapp i Linux

Du måste ha en funktionsapp som värd för körning av dina funktioner i Linux. Funktionsappen är en miljö för körning av funktionskoden. Där kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. Skapa en funktionsapp med kommandot [az functionapp create](/cli/azure/functionapp#az_functionapp_create) med en Linux App Service-plan. 

I följande kommando infogar du ett unikt funktionsappnamn istället för platshållaren `<app_name>` och lagringskontonamnet istället för `<storage_name>`. `<app_name>` används som DNS-standarddomän för funktionsappen. Därför måste namnet vara unikt bland alla appar i Azure. Parametern _deployment-source-url_ är en exempellagringsplats i GitHub som innehåller en HTTP-utlöst ”Hello World”-funktion.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-source-url https://github.com/Azure-Samples/functions-quickstart-linux
```
När funktionsappen har skapats och distribuerats visas information som liknar följande exempel i Azure CLI:

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

Eftersom `myAppServicePlan` är en Linux-plan används den inbyggda Docker-avbildningen för att skapa behållaren som kör funktionsappen i Linux. 

>[!NOTE]  
>Exempellagringsplatsen omfattar för närvarande två skriptfiler, [deploy.sh](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/deploy.sh) och [.deployment](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/.deployment). Filen .deployment talar om för distributionsprocessen att den ska använda deploy.sh som [anpassat distributionsskript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script). I den nuvarande förhandsversionen krävs skript för att distribuera funktionsappen i en Linux-avbildning.  

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
