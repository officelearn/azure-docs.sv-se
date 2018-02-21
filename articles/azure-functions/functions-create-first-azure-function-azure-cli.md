---
title: "Skapa din första funktion med Azure CLI | Microsoft Docs"
description: "Lär dig hur du skapar din första Azure-funktion för serverfri körning med Azure CLI."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 01/24/2018
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 2088844693748f090a67ad56f9b5fba4514d1282
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="create-your-first-function-using-the-azure-cli"></a>Skapa din första funktion med Azure CLI

I den här snabbstarten får du hjälp med att skapa din första funktion i Azure Functions. Du kan använda Azure CLI till att skapa en funktionsapp, som är den [serverfria](https://azure.microsoft.com/overview/serverless-computing/) infrastruktur som är värd för funktionen. Själva funktionskoden distribueras från en GitHub-exempellagringsplats.    

Du kan följa stegen nedan på en Mac-, Windows- eller Linux-dator. 

## <a name="prerequisites"></a>Nödvändiga komponenter 

Innan du kör exemplet måste du ha följande:

+ Ett aktivt [GitHub](https://github.com)-konto. 
+ En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 


[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner. Funktionsappen är en miljö för serverfri körning av funktionskoden. Där kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. Skapa en funktionsapp med kommandot [az functionapp create](/cli/azure/functionapp#az_functionapp_create). 

I följande kommando infogar du ett unikt funktionsappnamn istället för platshållaren `<app_name>` och lagringskontonamnet istället för `<storage_name>`. `<app_name>` används som DNS-standarddomän för funktionsappen. Därför måste namnet vara unikt bland alla appar i Azure. Parametern _deployment-source-url_ är en exempellagringsplats i GitHub som innehåller en HTTP-utlöst ”Hello World”-funktion.

```azurecli-interactive
az functionapp create --deployment-source-url https://github.com/Azure-Samples/functions-quickstart  \
--resource-group myResourceGroup --consumption-plan-location westeurope \
--name <app_name> --storage-account  <storage_name>  
```
Genom att konfigurera parametern _consumption-plan-location_ anger du att funktionsappen ska använda en förbrukningsplan som värd. I det här prenumerationsavtalet läggs resurser till dynamiskt när funktionerna har behov av det och du betalar endast när funktionerna används. Mer information finns i [Välja rätt värdplan](functions-scale.md). 

När funktionsappen har skapats visas information som liknar följande exempel i Azure CLI:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
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

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
