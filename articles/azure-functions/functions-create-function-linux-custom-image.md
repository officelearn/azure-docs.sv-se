---
title: Skapa en funktion i Linux med en anpassad avbildning (förhandsversion) | Microsoft Docs
description: Lär dig hur du skapar en Azure Functions som körs på en anpassad Linux-avbildning.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 10/19/2018
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: de15d1f8c268e80ac1659c53a141ec39cc6d3cb8
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51564817"
---
# <a name="create-a-function-on-linux-using-a-custom-image-preview"></a>Skapa en funktion i Linux med en anpassad avbildning (förhandsversion)

Med Azure Functions kan Linux användas som värd för funktionerna i en anpassad container. Du kan också använda [en standardcontainer i Azure App Service som värd](functions-create-first-azure-function-azure-cli-linux.md). Den här funktionen är för närvarande i förhandsversion och kräver [Functions 2.0-runtime](functions-versions.md).

I den här självstudiekursen lär du dig hur du distribuerar funktioner i Azure som en anpassad Docker-avbildning. Det här mönstret är användbart när du behöver anpassa den inbyggda containeravbildningen i App Service. Du kanske vill använda en anpassad avbildning när dina funktioner kräver en viss språkversion eller särskilda beroenden eller konfigurationer som inte är tillgängliga inom den inbyggda avbildningen.

Den här kursen går igen hur du använder Azure Functions Core Tools för att skapa en funktion i en anpassad Linux-avbildning. Du publicerar den här avbildningen i en funktionsapp i Azure, som har skapats med hjälp av Azure CLI.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en funktionsapp och Dockerfile med hjälp av Core Tools.
> * skapa en anpassad avbildning med Docker
> * Publicera en anpassad avbildning till ett containerregister.
> * skapa ett Azure Storage-konto
> * skapa en Linux App Service-plan
> * distribuera en funktionsapp från Docker Hub
> * lägga till programinställningar i funktionsappen.

Följande steg kan användas på en Mac-, Windows- eller Linux-dator.  

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du kör exemplet måste du ha följande:

* Installera [Azure Core Tools version 2.x](functions-run-local.md#v2).

* Installera [Azure CLI]( /cli/azure/install-azure-cli). Den här artikeln kräver Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har.  
Du kan också använda [Azure Cloud Shell](https://shell.azure.com/bash).

* En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Skapa det lokala funktionsapprojektet

Kör följande kommando från kommandoraden för att skapa ett funktionsapprojekt i mappen `MyFunctionProj` i den aktuella lokala katalogen.

```bash
func init MyFunctionProj --docker
```

När du inkluderar alternativet `--docker` skapas en Dockerfile för projektet. Den här filen används till att skapa en anpassad container där projektet körs. Vilken basavbildning som används beror på vilket arbetskörningsspråk som väljs.  

Vid uppmaning väljer du en arbetskörning från följande språk:

* `dotnet`: skapar ett .NET-klassbiblioteksprojekt (.csproj).
* `node`: skapar ett JavaScript-projekt.

När kommandot körs visas något i stil med följande utdata:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing Dockerfile
```

Använd följande kommando för att navigera till den nya `MyFunctionProj`-projektmappen.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-the-image-from-the-docker-file"></a>Skapa avbildningen från Docker-filen

Ta en titt på _Dockerfile_ i rotmappen för projektet. Den här filen beskriver vilken miljö som krävs för att köra funktionsappen på Linux. Följande exempel i en Dockerfile som skapar en container som kör en funktionsapp på JavaScript-arbetskörningen (Node.js): 

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> När ett privat containerregister används som värd för en avbildning ska du lägga till anslutningsinställningarna i funktionsappen med **ENV**-variabler i Dockerfile. Eftersom den här kursen inte kan garantera att du använder ett privat register, läggs anslutningsinställningarna till [efter distributionen med hjälp av Azure CLI](#configure-the-function-app) som en säkerhetsåtgärd.

### <a name="run-the-build-command"></a>Kör kommandot `build`
I rotmappen kör du kommandot [docker build](https://docs.docker.com/engine/reference/commandline/build/) och anger ett namn, `mydockerimage`, och en tagg, `v1.0.0`. Ersätt `<docker-id>` med ditt konto-ID för Docker Hub. Det här kommandot skapar Docker-avbildningen för containern.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

När kommandot körs visas något som liknar följande utdata, som i det här fallet är för en JavaScript-arbetskörning:

```bash
Sending build context to Docker daemon  17.41kB
Step 1/3 : FROM mcr.microsoft.com/azure-functions/node:2.0
2.0: Pulling from azure-functions/node
802b00ed6f79: Pull complete
44580ea7a636: Pull complete
73eebe8d57f9: Pull complete
3d82a67477c2: Pull complete
8bd51cd50290: Pull complete
7bd755353966: Pull complete
Digest: sha256:480e969821e9befe7c61dda353f63298f2c4b109e13032df5518e92540ea1d08
Status: Downloaded newer image for mcr.microsoft.com/azure-functions/node:2.0
 ---> 7c71671b838f
Step 2/3 : ENV AzureWebJobsScriptRoot=/home/site/wwwroot
 ---> Running in ed1e5809f0b7
Removing intermediate container ed1e5809f0b7
 ---> 39d9c341368a
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5e196215935a
Successfully built 5e196215935a
Successfully tagged <docker-id>/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>Testa avbildningen lokalt
Kontrollera att avbildningen du har skapat fungerar genom att köra Docker-avbildningen i en lokal container. Utfärda kommandot [docker run](https://docs.docker.com/engine/reference/commandline/run/) och skicka avbildningens namn och tagg till det. Kom ihåg att ange porten med argumentet `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Kontrollera medan den anpassade avbildningen körs i en lokal Docker-container att funktionsappen och containern fungerar som de ska genom att gå till <http://localhost:8080>.

![Testa funktionsappen lokalt.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

Du kan testa din funktion igen, den här gången i den lokala containern med hjälp av följande URL:

`http://localhost:8080/api/myhttptrigger?name=<yourname>`

Stoppa körningen när du har verifierat funktionsappen i containern. Nu kan överföra den anpassade avbildningen till ditt Docker Hub-konto.

## <a name="push-the-custom-image-to-docker-hub"></a>Push-överför den anpassade avbildningen till Docker Hub

Ett register är ett program som är värd för avbildningar och tillhandahåller tjänster för avbildningar och containrar. För att kunna dela avbildningen måste du push-överföra den till ett register. Docker Hub är ett register för Docker-avbildningar som gör att du kan vara värd för dina egna lagringsplatser, antingen offentliga eller privata.

Innan du kan push-överföra en avbildning måste du logga in på Docker Hub med kommandot [docker login](https://docs.docker.com/engine/reference/commandline/login/). Ersätt `<docker-id>` med ditt kontonamn och ange ditt lösenord i konsolen när det efterfrågas. För andra alternativ för Docker Hub-lösenord, se [kommandodokumentationen om dockerinloggning](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

Ett meddelande om att inloggningen har slutförts bekräftar att du är inloggad. När du har loggat in push-överför du avbildningen till Docker Hub med kommandot [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Verifiera att push-överföringen lyckades genom att undersöka kommandots utdata.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from <docker-id>/mydockerimage
e7796c35add2: Mounted from <docker-id>/mydockerimage
ae9a05b85848: Mounted from <docker-id>/mydockerimage
45c86e20670d: Mounted from <docker-id>/mydockerimage
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 1796
```

Nu kan du använda avbildningen som distributionskälla för en ny funktionsapp i Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Skapa en Linux App Service-plan

Linux-värd för funktioner stöds för närvarande inte i förbrukningsplaner. Du måste värdbasera Linux-containerapparna i en Linux App Service-plan. Mer information om värdfunktioner finns i [Azure Functions hosting plans comparison](functions-scale.md) (Jämförelse av Azure Functions-värdplaner).

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-and-deploy-the-custom-image"></a>Skapa och distribuera den anpassade avbildningen

Funktionsappen är värd för körningen av dina funktioner. Skapa en funktionsapp från en Docker Hub-avbildning med kommandot [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

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

Funktionen behöver anslutningssträngen för att ansluta till standardlagringskontot. När du publicerar den anpassade avbildningen i ett privat containerkonto bör du i stället använda dessa programinställningar som miljövariabler i Dockerfile med hjälp av [ENV-instruktionen](https://docs.docker.com/engine/reference/builder/#env) eller något liknande.

I det här fallet är `<storage_account>` namnet på det lagringskonto du skapade. Visa anslutningssträngen med kommandot [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Lägg till dessa programinställningar i funktionsappen med kommandot [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> Om containern är privat skulle du även behöva ange följande programinställningar  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> Du måste stoppa och sedan starta funktionsappen för att dessa värden ska hämtas

Nu kan du testa dina funktioner som körs på Linux i Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en funktionsapp och Dockerfile med hjälp av Core Tools.
> * skapa en anpassad avbildning med Docker
> * Publicera en anpassad avbildning till ett containerregister.
> * skapa ett Azure Storage-konto
> * skapa en Linux App Service-plan
> * distribuera en funktionsapp från Docker Hub
> * lägga till programinställningar i funktionsappen.

Lär dig hur du aktiverar kontinuerliga integrationsfunktioner i Apptjänst-kärnplattformen. Du kan konfigurera din funktionsapp så att containern omdistribueras när du uppdaterar avbildningen i Docker-hubben.

> [!div class="nextstepaction"] 
> [Kontinuerlig distribution med Web App for Containers](../app-service/containers/app-service-linux-ci-cd.md)
