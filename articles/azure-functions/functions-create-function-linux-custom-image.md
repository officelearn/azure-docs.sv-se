---
title: "Skapa en funktion i Linux med en anpassad avbildning (förhandsversion) | Microsoft Docs"
description: "Lär dig mer om att skapa Azure-funktioner som körs på en anpassad avbildning i Linux."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.date: 11/08/2017
ms.topic: article
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
ms.openlocfilehash: 40fbbd0ec020320b26ed343aec8ac31d60b646dc
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-function-on-linux-using-a-custom-image-preview"></a>Skapa en funktion i Linux med en anpassad avbildning (förhandsgranskning)

Azure Functions kan du vara värd för dina funktioner på Linux i din egen anpassade container. Den här funktionen är för närvarande under förhandsgranskning. Du kan också [värd i Azure App Service standardbehållaren](functions-create-first-azure-function-azure-cli-linux.md).  

Lär dig hur du distribuerar en funktionsapp som en anpassad Docker-avbildning i den här självstudiekursen. Det här mönstret är användbart när du behöver anpassa inbyggda Apptjänst behållare avbildningen. Du kanske vill använda en anpassad avbildning när dina funktioner måste en viss språkversion eller kräver en viss beroende eller en konfiguration som inte är inom den inbyggda avbildningen.

Den här kursen får du veta hur du använder Azure Functions för att skapa och skicka en anpassad avbildning till Docker-hubben. Du sedan använda den här avbildningen som distributionskälla för en funktionsapp som körs på Linux. Du kan använda Docker för att bygga och push-avbildningen. Du kan använda Azure CLI för att skapa en funktionsapp och distribuera avbildningen från Docker-hubben. 

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en anpassad avbildning med Docker.
> * Publicera en anpassad avbildning till en behållare för registret. 
> * Skapa ett Azure Storage-konto. 
> * Skapa en Linux App Service-plan. 
> * Distribuera en funktionsapp från Docker-hubben.
> * Lägg till tillämpningsinställningar funktionen appen. 

Följande steg kan användas på en Mac, Windows eller Linux-dator.  

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* [Git](https://git-scm.com/downloads)
* En aktiv [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Docker](https://docs.docker.com/get-started/#setup)
* En [Docker-hubb-konto](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Hämta exemplet

Kör följande kommando för att klona exempel app lagringsplatsen till den lokala datorn och sedan ändra till katalogen som innehåller exempelkoden i ett terminalfönster.

```bash
git clone https://github.com/Azure-Samples/functions-linux-custom-image.git --config core.autocrlf=input
cd functions-linux-custom-image
```

## <a name="build-the-image-from-the-docker-file"></a>Skapa bilden från filen Docker

I den här Git-lagringsplatsen, ta en titt på den _Dockerfile_. Den här filen beskriver den miljö som krävs för att köra funktionsapp-på Linux. 

```docker
# Base the image on the built-in Azure Functions Linux image.
FROM microsoft/azure-functions-runtime:2.0.0-jessie
ENV AzureWebJobsScriptRoot=/home/site/wwwroot

# Add files from this repo to the root site folder.
COPY . /home/site/wwwroot 
```
>[!NOTE]
> När värd för en bild i ett privat behållaren register, bör du lägga till anslutningsinställningarna appen funktionen med hjälp av **ENV** variabler i Dockerfile. Eftersom den här kursen inte kan garantera att du använder ett privat register, anslutningsinställningarna är [läggas till efter distributionen med hjälp av Azure CLI](#configure-the-function-app) som en säkerhetsåtgärd.   

### <a name="run-the-build-command"></a>Kör kommandot Build
För att skapa filen Docker kör den `docker build` kommando och ange ett namn, `mydockerimage`, och tagg, `v1.0.0`. Ersätt `<docker-id>` konto med hubben Docker ID.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Kommandot resulterar utdata som liknar följande:

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

### <a name="test-the-image-locally"></a>Testbild lokalt
Kontrollera att den inbyggda avbildningen fungerar genom att köra Docker-avbildning i en lokal behållare. Problem i [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando och ange namn och taggen för avbildningen till den. Se till att ange den port som använder den `-p` argumentet.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Med den anpassade avbildningen körs i en lokal dockerbehållare Kontrollera funktionsapp och behållare fungerar som de ska genom att bläddra till <http://localhost: 8080>.

![Testa funktionen appen lokalt.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

Stoppa körning när du hav verifierats funktionsapp i behållaren. Nu kan skicka du den anpassade avbildningen till Docker-hubb-konto.

## <a name="push-the-custom-image-to-docker-hub"></a>Push-den anpassade avbildningen till Docker-hubb

Ett register är ett program som är värd för avbildningar och tillhandahåller tjänster avbildningen och behållare. För att dela din bild skicka du den till ett register. Docker-hubben är ett register för Docker-avbildningar som gör att du kan vara värd för dina egna databaser, offentlig eller privat. 

Innan du kan trycka på en bild, du måste logga in till Docker-hubb med hjälp av den [docker inloggning](https://docs.docker.com/engine/reference/commandline/login/) kommando. Ersätt `<docker-id>` med ditt kontonamn och ange ditt lösenord i konsolen i Kommandotolken. Andra Docker-hubb lösenordsalternativ finns i [docker inloggningen kommandot dokumentationen](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id> 
```

Meddelandet ”inloggningen har slutförts” bekräftar att du är inloggad. När du har loggat in push avbildningen till Docker-hubben med hjälp av den [docker push](https://docs.docker.com/engine/reference/commandline/push/) kommando.

```bash
docker push <docker-id>/mydockerimage:v1.0.0 .
```

Kontrollera att push lyckades genom att undersöka kommandot utdata.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from microsoft/azure-functions-runtime
e7796c35add2: Mounted from microsoft/azure-functions-runtime
ae9a05b85848: Mounted from microsoft/azure-functions-runtime
45c86e20670d: Mounted from microsoft/azure-functions-runtime
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 2422
```
Nu, kan du använda den här avbildningen som distributionskälla för en ny funktionsapp i Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver Azure CLI version 2.0 eller senare i det här avsnittet. Kör `az --version` att hitta den version du har. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Skapa en Linux App Service-plan

Linux-värd för funktioner stöds för närvarande inte på förbrukning planer. Du måste köra på en Linux App Service-plan. Mer information om värd finns [Azure Functions värd planer jämförelse](functions-scale.md). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]


## <a name="create-and-deploy-the-custom-image"></a>Skapa och distribuera den anpassade avbildningen

Funktionen appen är värd för körningen av dina funktioner. Skapa en funktionsapp från en Docker-hubb-avbildning med hjälp av den [az functionapp skapa](/cli/azure/functionapp#create) kommando. 

Ersätt namnet på appen en unik funktion där du ser i följande kommando i `<app_name>` platshållare och lagringskontot namn för `<storage_name>`. `<app_name>` används som DNS-standarddomän för funktionsappen. Därför måste namnet vara unikt bland alla appar i Azure. Som tidigare `<docker-id>` är namnet på ditt Docker.

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

Den _distribution-behållaren-bild-name_ parametern anger det bild på Docker-hubb för att skapa funktionen appen. 


## <a name="configure-the-function-app"></a>Konfigurera funktionen appen

Funktionen måste anslutningssträngen att ansluta till standardkontot för lagring. När du publicerar den anpassade avbildningen till ett privat behållare konto bör i stället använda dessa inställningar för program som miljövariabler i Dockerfile med hjälp av [ENV instruktion](https://docs.docker.com/engine/reference/builder/#env), eller motsvarande. 

I det här fallet `<storage_account>` är namnet på lagringskontot som du skapade. Hämta anslutningssträngen med den [az lagring konto visa anslutningssträng](/cli/azure/storage/account#show-connection-string) kommando. Lägg till dessa programinställningar i funktionsapp med den [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) kommando.

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
> * Skapa en anpassad avbildning med Docker.
> * Publicera en anpassad avbildning till en behållare för registret. 
> * Skapa ett Azure Storage-konto. 
> * Skapa en Linux App Service-plan. 
> * Distribuera en funktionsapp från Docker-hubben.
> * Lägg till tillämpningsinställningar funktionen appen.

Mer information om hur du utvecklar Azure Functions lokalt med hjälp av Azure Functions grundläggande verktyg.

> [!div class="nextstepaction"] 
> [Platskod och testa Azure Functions lokalt](functions-run-local.md)
