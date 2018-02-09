---
title: "Skapa en funktion i Linux med en anpassad avbildning (förhandsversion) | Microsoft Docs"
description: "Lär dig hur du skapar en Azure Functions som körs på en anpassad Linux-avbildning."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 555d05c6cd5e804e5f80ecb8df77237fd8270105
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-function-on-linux-using-a-custom-image-preview"></a>Skapa en funktion i Linux med en anpassad avbildning (förhandsversion)

Med Azure Functions kan Linux användas som värd för funktionerna i en anpassad behållare. Du kan också använda [en standardbehållare i Azure App Service som värd](functions-create-first-azure-function-azure-cli-linux.md). Den här funktionen ges för närvarande i förhandsversion och kräver [Functions 2.0 runtime](functions-versions.md), som också finns som förhandsversion.

I den här självstudiekursen lär du dig att distribuera en funktionsapp som en anpassad Docker-avbildning. Det här mönstret är användbart när du behöver anpassa den inbyggda behållaravbildningen i App Service. Du kanske vill använda en anpassad avbildning när dina funktioner kräver en viss språkversion eller särskilda beroenden eller konfigurationer som inte är tillgängliga inom den inbyggda avbildningen.

Den här kursen förklarar hur du använder Azure Functions för att skapa och överföra en anpassad avbildning till Docker Hub. Sedan använder du avbildningen som distributionskälla för en funktionsapp som körs på Linux. Du använder Docker för att bygga och överföra avbildningen. Du använder Azure CLI för att skapa en funktionsapp och distribuera avbildningen från Docker Hub. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * skapa en anpassad avbildning med Docker
> * publicera en anpassad avbildning till ett behållarregister 
> * skapa ett Azure Storage-konto 
> * skapa en Linux App Service-plan 
> * distribuera en funktionsapp från Docker Hub
> * lägga till programinställningar i funktionsappen. 

Följande steg kan användas på en Mac-, Windows- eller Linux-dator.  

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:

* [Git](https://git-scm.com/downloads)
* En aktiv [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Docker](https://docs.docker.com/get-started/#setup)
* Ett [Docker Hub-konto](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Hämta exemplet

Öppna terminalfönstret och kör följande kommando för att klona exempelappens lagringsplats till din lokala dator. Ändra sedan till katalogen som innehåller exempelkoden.

```bash
git clone https://github.com/Azure-Samples/functions-linux-custom-image.git --config core.autocrlf=input
cd functions-linux-custom-image
```

## <a name="build-the-image-from-the-docker-file"></a>Skapa avbildningen från Docker-filen

På Git-lagringsplatsen tar du en titt på _Dockerfile_. Den här filen beskriver vilken miljö som krävs för att köra funktionsappen på Linux. 

```docker
# Base the image on the built-in Azure Functions Linux image.
FROM microsoft/azure-functions-runtime:2.0.0-jessie
ENV AzureWebJobsScriptRoot=/home/site/wwwroot

# Add files from this repo to the root site folder.
COPY . /home/site/wwwroot 
```
>[!NOTE]
> När ett privat behållarregister används som värd för en avbildning ska du lägga till anslutningsinställningarna i funktionsappen med **ENV**-variabler i Dockerfile. Eftersom den här kursen inte kan garantera att du använder ett privat register, läggs anslutningsinställningarna till [efter distributionen med hjälp av Azure CLI](#configure-the-function-app) som en säkerhetsåtgärd.   

### <a name="run-the-build-command"></a>Köra Build-kommandot
Skapa Docker-avbildningen genom att köra kommandot `docker build` och ange ett namn, `mydockerimage`, och en tagg, `v1.0.0`. Ersätt `<docker-id>` med ditt konto-ID för Docker Hub.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Kommandot producerar utdata som liknar följande:

```bash
Sending build context to Docker daemon  169.5kB
Step 1/3 : FROM microsoft/azure-functions-runtime:v2.0.0-jessie
v2.0.0-jessie: Pulling from microsoft/azure-functions-runtime
b178b12f7913: Pull complete
2d9ce077a781: Pull complete
4775d4ba55c8: Pull complete
Digest: sha256:073f45fc167b3b5c6642ef4b3c99064430d6b17507095...
Status: Downloaded newer image for microsoft/azure-functions-runtime:v2.0.0-jessie
 ---> 217799efa500
Step 2/3 : ENV AzureWebJobsScriptRoot /home/site/wwwroot
 ---> Running in 528fa2077d17
 ---> 7cc6323b8ae0
Removing intermediate container 528fa2077d17
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5bdac9878423
Successfully built 5bdac9878423
Successfully tagged ggailey777/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>Testa avbildningen lokalt
Kontrollera att avbildningen fungerar genom att köra Docker-avbildningen i en lokal behållare. Utfärda kommandot [docker run](https://docs.docker.com/engine/reference/commandline/run/) och skicka avbildningens namn och tagg till det. Kom ihåg att ange porten med argumentet `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Kontrollera medan den anpassade avbildningen körs i en lokal Docker-behållare att funktionsappen och behållaren fungerar som de ska genom att gå till <http://localhost:8080>.

![Testa funktionsappen lokalt.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

Stoppa körningen när du har verifierat funktionsappen i behållaren. Nu kan överföra den anpassade avbildningen till ditt Docker Hub-konto.

## <a name="push-the-custom-image-to-docker-hub"></a>Push-överför den anpassade avbildningen till Docker Hub

Ett register är ett program som är värd för avbildningar och tillhandahåller tjänster för avbildningar och behållare. För att dela avbildningen måste du push-överföra den till ett register. Docker Hub är ett register för Docker-avbildningar som gör att du kan vara värd för dina egna lagringsplatser, antingen offentliga eller privata. 

Innan du kan push-överföra en avbildning måste du logga in på Docker Hub med kommandot [docker login](https://docs.docker.com/engine/reference/commandline/login/). Ersätt `<docker-id>` med ditt kontonamn och ange ditt lösenord i konsolen när det efterfrågas. För andra alternativ för Docker Hub-lösenord, se [kommandodokumentationen om dockerinloggning](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id> 
```

Ett meddelande om att inloggningen har slutförts bekräftar att du är inloggad. När du har loggat in push-överför du avbildningen till Docker Hub med kommandot [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0 .
```

Verifiera att push-överföringen lyckades genom att undersöka kommandots utdata.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from microsoft/azure-functions-runtime
e7796c35add2: Mounted from microsoft/azure-functions-runtime
ae9a05b85848: Mounted from microsoft/azure-functions-runtime
45c86e20670d: Mounted from microsoft/azure-functions-runtime
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 2422
```
Nu kan du använda avbildningen som distributionskälla för en ny funktionsapp i Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du använda Azure CLI version 2.0.21 eller senare. Kör `az --version` för att se vilken version du har. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Skapa en Linux App Service-plan

Linux-värd för funktioner stöds för närvarande inte i förbrukningsplaner. Du måste köra en Linux App Service-plan. Mer information om värdfunktioner finns i [Azure Functions hosting plans comparison](functions-scale.md) (Jämförelse av Azure Functions-värdplaner). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]


## <a name="create-and-deploy-the-custom-image"></a>Skapa och distribuera den anpassade avbildningen

Funktionsappen är värd för körningen av dina funktioner. Skapa en funktionsapp från en Docker Hub-avbildning med kommandot [az functionapp create](/cli/azure/functionapp#az_functionapp_create). 

I följande kommando infogar du ett unikt funktionsappnamn istället för platshållaren `<app_name>` och lagringskontonamnet istället för `<storage_name>`. `<app_name>` används som DNS-standarddomän för funktionsappen. Därför måste namnet vara unikt bland alla appar i Azure. Precis som tidigare är `<docker-id>` ditt Docker-kontonamn.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0 
```
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

Parametern _deployment-container-image-name_ anger vilken avbildning på Docker Hub som ska användas för att skapa funktionsappen. 


## <a name="configure-the-function-app"></a>Konfigurera funktionsappen

Funktionen behöver anslutningssträngen för att ansluta till standardlagringskontot. När du publicerar den anpassade avbildningen i ett privat behållarkonto bör du i stället använda dessa programinställningar som miljövariabler i Dockerfile med hjälp av [ENV-instruktionen](https://docs.docker.com/engine/reference/builder/#env) eller motsvarande. 

I det här fallet är `<storage_account>` namnet på det lagringskonto du skapade. Visa anslutningssträngen med kommandot [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Lägg till dessa programinställningar i funktionsappen med kommandot [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

Nu kan du testa dina funktioner som körs på Linux i Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * skapa en anpassad avbildning med Docker
> * publicera en anpassad avbildning till ett behållarregister 
> * skapa ett Azure Storage-konto 
> * skapa en Linux App Service-plan 
> * distribuera en funktionsapp från Docker Hub
> * lägga till programinställningar i funktionsappen.

Läs mer om hur du utvecklar Azure Functions lokalt med hjälp av Azure Functions Core Tools.

> [!div class="nextstepaction"] 
> [Koda och testa Azure Functions lokalt](functions-run-local.md)
