---
title: "Skapa din första funktion med Azure CLI | Microsoft Docs"
description: "Lär dig hur du skapar din första Azure-funktion för serverfri körning med Azure CLI."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 05/02/2017
ms.topic: hero-article
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: erikre
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 2292b35819c5a98b690041e10f6e6d1a93fa7837
ms.contentlocale: sv-se
ms.lasthandoff: 06/20/2017

---

# <a name="create-your-first-function-using-the-azure-cli"></a>Skapa din första funktion med Azure CLI

I den här snabbstartsguiden får du hjälp att skapa din första funktion i Azure Functions. Du kan använda Azure CLI till att skapa en funktionsapp, som är den serverfria infrastruktur som är värd för funktionen. Själva funktionskoden distribueras från en GitHub-exempellagringsplats.    

Du kan följa stegen nedan på en Mac-, Windows- eller Linux-dator. 

## <a name="prerequisites"></a>Krav 

Innan du kör exemplet måste du ha följande:

+ Ett aktivt [GitHub](https://github.com)-konto. 
+ En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create). En Azure-resursgrupp är en logisk behållare som Azure-resurser (t.ex. funktionsappar, databaser och lagringskonton) distribueras och hanteras i.

I följande exempel skapas en resursgrupp med namnet `myResourceGroup`:

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
## <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

I funktioner används ett Azure Storage-konto till att lagra status och annan information om dina funktioner. Skapa ett lagringskonto i resursgruppen du skapade med hjälp av kommandot [az storage account create](/cli/azure/storage/account#create).

I följande kommando infogar du ditt globalt unika lagringskontonamn istället för platshållaren `<storage_name>`. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

När lagringskontot har skapats visas information som liknar följande exempel i Azure CLI:

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste ha en funktionsapp som värd för körning av dina funktioner. Funktionsappen är en miljö för serverfri körning av funktionskoden. Där kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. Skapa en funktionsapp med kommandot [az functionapp create](/cli/azure/functionapp#create). 

I följande kommando infogar du ditt unika funktionsappnamn istället för platshållaren `<app_name>` och lagringskontonamnet istället för `<storage_name>`. `<app_name>` används som DNS-standarddomän för funktionsappen. Därför måste namnet vara unikt bland alla appar i Azure. 

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup --consumption-plan-location westeurope
```
Som standard skapas en funktionsapp med värdplanen Consumption, vilket innebär att resurser läggs till dynamiskt när de behövs i dina funktioner och att du bara betalar när funktionerna körs. Mer information finns i [Välja rätt värdplan](functions-scale.md). 

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

Nu när du har en funktionsapp kan du distribuera den faktiska funktionskoden från GitHub-exempellagringsplatsen.

## <a name="deploy-your-function-code"></a>Distribuera din funktionskod  

Det finns flera sätt att skapa funktionskoden i din nya funktionsapp. I det här ämnet ansluter vi till en exempellagringsplats i GitHub. Precis som tidigare ersätter du platshållaren `<app_name>` med namnet på den funktionsapp du skapade. 

```azurecli-interactive
az functionapp deployment source config --name <app_name> --resource-group myResourceGroup --repo-url https://github.com/Azure-Samples/functions-quickstart --branch master --manual-integration
```
När distributionskällan har angetts visas information som liknar följande exempel i Azure CLI (nullvärden är borttagna för att öka läsbarheten):

```json
{
  "branch": "master",
  "deploymentRollbackEnabled": false,
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myResourceGroup/...",
  "isManualIntegration": true,
  "isMercurial": false,
  "location": "West Europe",
  "name": "quickstart",
  "repoUrl": "https://github.com/Azure-Samples/functions-quickstart",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.Web/sites/sourcecontrols"
}
```

## <a name="test-the-function"></a>Testa funktionen

Använd cURL till att testa den distribuerade funktionen på en Mac- eller Linux-dator, eller Bash i Windows. Kör följande cURL-kommando och ersätt platshållaren `<app_name>` med namnet på din funktionsapp. Lägg till frågesträngen `&name=<yourname>` i webbadressen.

```bash
curl http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```  

![Funktionssvaret visas i en webbläsare.](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-curl.png)  

Om du inte har cURL tillgängligt på kommandoraden anger du bara samma webbadress i webbläsarens adressfält. På samma sätt ersätter du platshållaren `<app_name>` med namnet på funktionsappen och lägger till frågesträngen `&name=<yourname>` i webbadressen. 

    http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
   
![Funktionssvaret visas i en webbläsare.](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-browser.png)  

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter eller med självstudierna ska du inte rensa resurserna som skapas i denna snabbstart. Om du inte planerar att fortsätta kan du använda kommandona nedan för att ta bort alla resurser som har skapats i den här snabbstarten:

```azurecli-interactive
az group delete --name myResourceGroup
```
Skriv `y` när du uppmanas till detta.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

