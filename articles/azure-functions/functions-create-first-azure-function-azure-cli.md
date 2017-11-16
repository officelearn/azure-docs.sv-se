---
title: "Skapa din första funktion med Azure CLI | Microsoft Docs"
description: "Lär dig hur du skapar din första Azure-funktion för serverfri körning med Azure CLI."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/08/2017
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 4356d00b2694224f52a9359cd4a57d3a70a34d18
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="create-your-first-function-using-the-azure-cli"></a>Skapa din första funktion med Azure CLI

Det här avsnittet för Snabbstart vägleder dig igenom hur du använder Azure Functions för att skapa din första funktion. Du använder Azure CLI för att skapa en funktionsapp, vilket är den [serverlösa](https://azure.microsoft.com/overview/serverless-computing/) infrastruktur som är värd för din funktion. Själva funktionskoden distribueras från en GitHub-exempellagringsplats.    

Du kan följa stegen nedan på en Mac-, Windows- eller Linux-dator. 

## <a name="prerequisites"></a>Krav 

Innan du kör exemplet måste du ha följande:

+ Ett aktivt [GitHub](https://github.com)-konto. 
+ En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver Azure CLI version 2.0 eller senare i det här avsnittet. Kör `az --version` att hitta den version du har. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 


[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner. Funktionsappen är en miljö för serverfri körning av funktionskoden. Där kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. Skapa en funktionsapp med kommandot [az functionapp create](/cli/azure/functionapp#create). 

Ersätt namnet på appen en unik funktion där du ser i följande kommando i `<app_name>` platshållare och lagringskontot namn för `<storage_name>`. `<app_name>` används som DNS-standarddomän för funktionsappen. Därför måste namnet vara unikt bland alla appar i Azure. Den _distribution källadress_ parametern är en exempel-lagringsplatsen i GitHub som innehåller ”Hello World” HTTP aktiveras funktionen.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--consumption-plan-location westeurope --deployment-source-url https://github.com/Azure-Samples/functions-quickstart
```
Ange den _förbrukning plan plats_ parametern innebär att funktionen är värd för appen i en värd plan förbrukning. Resurser läggs till dynamiskt som krävs av dina funktioner och du betalar endast när funktioner som körs i den här planen. Mer information finns i [Välja rätt värdplan](functions-scale.md). 

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