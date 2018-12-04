---
title: Skapa en funktionsapp i Linux i en Azure App Service-plan
description: Lär dig hur du skapar en funktionsapp som körs på Linux i en App Service-plan med hjälp av Azure CLI.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: d9800ff3fc82636c5cae12167738667ec84326ee
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52857042"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan-preview"></a>Skapa en funktionsapp i Linux i en Azure App Service-plan (förhandsversion)

Med hjälp av Azure Functions kan Linux användas som värd för funktionerna i en Azure App Service-standardcontainer. Den här artikeln får du veta hur du använder Azure CLI för att skapa en funktionsapp som värd för Linux i Azure som körs i en [App Service-plan](functions-scale.md#app-service-plan). Du kan även [använda en egen anpassad container](functions-create-function-linux-custom-image.md). Linux-värd är för närvarande i förhandsversion.

Du är ansvarig för att skala din funktionsapp i en App Service-plan. Om du vill dra nytta av serverlös funktionerna i Azure Functions, du kan också vara värd för dina funktioner på Linux i en [förbrukningsplan](functions-scale.md#consumption-plan).

Du kan följa stegen nedan på en Mac-, Windows- eller Linux-dator.

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att slutföra den här snabbstarten:

+ En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du använda Azure CLI version 2.0.21 eller senare. Kör `az --version` för att se vilken version du har. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Skapa en Linux App Service-plan

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Skapa en funktionsapp i Linux

Du måste ha en funktionsapp som värd för körning av dina funktioner i Linux. Funktionsappen är en miljö för körning av funktionskoden. Där kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. Skapa en funktionsapp med kommandot [az functionapp create](/cli/azure/functionapp#az-functionapp-create) med en Linux App Service-plan.

I följande kommando infogar du ett unikt funktionsappnamn istället för platshållaren `<app_name>` och lagringskontonamnet istället för `<storage_name>`. `<app_name>` används som DNS-standarddomän för funktionsappen. Därför måste namnet vara unikt bland alla appar i Azure. Du bör också ange den `<language>` runtime för din funktionsapp från `dotnet` (C#), `node` (JavaScript), eller `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --plan myAppServicePlan \
--name <app_name> --storage-account  <storage_name> --runtime <language>
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

Eftersom `myAppServicePlan` är en Linux-plan används den inbyggda Docker-avbildningen för att skapa containern som kör funktionsappen i Linux.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources-simple.md)]

## <a name="next-steps"></a>Nästa steg

Den här artikeln visar hur du skapar en funktionsapp i Linux som ligger i Azure. Du kan nu [distribuera ett funktionsprojekt](https://docs.microsoft.com/cli/azure/functionapp/deployment/source?view=azure-cli-latest) till funktionsappen. Du kan använda Azure Functions Core Tools till [skapa ett funktionsprojekt](functions-run-local.md#create-a-local-functions-project) på din lokala dator och distribuera den till din nya funktionsapp i Linux.  

> [!div class="nextstepaction"] 
> [Koda och testa Azure Functions lokalt](functions-run-local.md)
