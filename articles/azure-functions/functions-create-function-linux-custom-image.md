---
title: Skapa Azure Functions på Linux med en anpassad avbildning
description: Lär dig hur du skapar en Azure Functions som körs på en anpassad Linux-avbildning.
author: ggailey777
ms.author: glenga
ms.date: 09/27/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
manager: gwallace
ms.openlocfilehash: 54d7dc4e57991f6b773169f539a86fdc8451cbba
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950377"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>Skapa en funktion i Linux med en anpassad avbildning

Med Azure Functions kan Linux användas som värd för funktionerna i en anpassad container. Du kan också använda [en standardcontainer i Azure App Service som värd](functions-create-first-azure-function-azure-cli-linux.md). Den här funktionen kräver [funktionerna 2. x runtime](functions-versions.md).

I den här självstudiekursen lär du dig hur du distribuerar funktioner i Azure som en anpassad Docker-avbildning. Det här mönstret är användbart när du behöver anpassa den inbyggda behållar avbildningen. Du kanske vill använda en anpassad avbildning när dina funktioner kräver en viss språkversion eller särskilda beroenden eller konfigurationer som inte är tillgängliga inom den inbyggda avbildningen. Bas avbildningar som stöds för Azure Functions finns i [Azure Functions Base images-lagrings platsen](https://hub.docker.com/_/microsoft-azure-functions-base). 

Den här kursen går igen hur du använder Azure Functions Core Tools för att skapa en funktion i en anpassad Linux-avbildning. Du publicerar den här avbildningen i en funktionsapp i Azure, som har skapats med hjälp av Azure CLI. Senare kan du uppdatera din funktion för att ansluta till Azure Queue Storage. Du kan också aktivera.  

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en funktionsapp och Dockerfile med hjälp av Core Tools.
> * skapa en anpassad avbildning med Docker
> * Publicera en anpassad avbildning till ett containerregister.
> * skapa ett Azure Storage-konto
> * Skapa en Premium hosting-plan.
> * distribuera en funktionsapp från Docker Hub
> * lägga till programinställningar i funktionsappen.
> * Aktivera kontinuerlig distribution.
> * Aktivera SSH-anslutningar till behållaren.
> * Lägg till en utgående bindning för kö Storage. 
> * Lägg till Application Insights övervakning.

Följande steg kan användas på en Mac-, Windows- eller Linux-dator. 

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du kör exemplet måste du ha följande:

* Installera [Azure Core Tools version 2.x](functions-run-local.md#v2).

* Installera [Azure CLI]( /cli/azure/install-azure-cli). Den här artikeln kräver Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har.  
Du kan också använda [Azure Cloud Shell](https://shell.azure.com/bash).

* En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="create-the-local-project"></a>Skapa det lokala projektet

Kör följande kommando från kommandoraden för att skapa ett funktionsapprojekt i mappen `MyFunctionProj` i den aktuella lokala katalogen. För ett python-projekt [måste du köra i en virtuell miljö](functions-create-first-function-python.md#create-and-activate-a-virtual-environment-optional).

```bash
func init MyFunctionProj --docker
```

När du inkluderar alternativet `--docker` skapas en Dockerfile för projektet. Den här filen används till att skapa en anpassad container där projektet körs. Vilken basavbildning som används beror på vilket arbetskörningsspråk som väljs.  

Vid uppmaning väljer du en arbetskörning från följande språk:

* `dotnet`: skapar ett biblioteks projekt för .NET Core-klass (. CSPROJ).
* `node`: skapar ett JavaScript-projekt.
* `python`: skapar ett Python-projekt.  

Använd följande kommando för att navigera till den nya `MyFunctionProj`-projektmappen.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-from-the-docker-file"></a>Bygg från Docker-filen

Ta en titt på _Dockerfile_ i rotmappen för projektet. Den här filen beskriver vilken miljö som krävs för att köra funktionsappen på Linux. Följande exempel i en Dockerfile som skapar en container som kör en funktionsapp på JavaScript-arbetskörningen (Node.js): 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> Den fullständiga listan med bas avbildningar som stöds för Azure Functions finns på [sidan för Azure Functions Base-avbildning](https://hub.docker.com/_/microsoft-azure-functions-base).

### <a name="run-the-build-command"></a>Kör kommandot `build`

I rotmappen kör du kommandot [docker build](https://docs.docker.com/engine/reference/commandline/build/) och anger ett namn, `mydockerimage`, och en tagg, `v1.0.0`. Ersätt `<docker-id>` med ditt konto-ID för Docker Hub. Det här kommandot skapar Docker-avbildningen för containern.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

När kommandot har slutförts kan du köra den nya behållaren lokalt.

### <a name="run-the-image-locally"></a>Kör avbildningen lokalt
Kontrollera att avbildningen du har skapat fungerar genom att köra Docker-avbildningen i en lokal container. Utfärda kommandot [docker run](https://docs.docker.com/engine/reference/commandline/run/) och skicka avbildningens namn och tagg till det. Kom ihåg att ange porten med argumentet `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Kontrollera medan den anpassade avbildningen körs i en lokal Docker-container att funktionsappen och containern fungerar som de ska genom att gå till <http://localhost:8080>.

![Kör Function-appen lokalt.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

> [!NOTE]
> Vid det här tillfället får du ett HTTP 401-felsvar när du försöker anropa en viss HTTP-funktion. Detta beror på att funktionen körs i den lokala behållaren som i Azure, vilket innebär att funktions nyckeln är obligatorisk. Eftersom behållaren ännu inte har publicerats till en Function-app finns det ingen funktions nyckel tillgänglig. Du kommer att se senare när du använder kärn verktyg för att publicera din behållare, funktions tangenterna visas för dig. Om du vill testa funktionen som körs i den lokala behållaren kan du ändra [auktoriseringsregeln](functions-bindings-http-webhook.md#authorization-keys) till `anonymous`. 

Stoppa körningen när du har verifierat funktionsappen i containern. Nu kan överföra den anpassade avbildningen till ditt Docker Hub-konto.

## <a name="push-to-docker-hub"></a>Push till Docker-hubb

Ett register är ett program som är värd för avbildningar och tillhandahåller tjänster för avbildningar och containrar. För att kunna dela avbildningen måste du push-överföra den till ett register. Docker Hub är ett register för Docker-avbildningar som gör att du kan vara värd för dina egna lagringsplatser, antingen offentliga eller privata.

Innan du kan push-överföra en avbildning måste du logga in på Docker Hub med kommandot [docker login](https://docs.docker.com/engine/reference/commandline/login/). Ersätt `<docker-id>` med ditt kontonamn och ange ditt lösenord i konsolen när det efterfrågas. För andra alternativ för Docker Hub-lösenord, se [kommandodokumentationen om dockerinloggning](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

Meddelandet "inloggningen lyckades" bekräftar att du är inloggad. När du har loggat in push-överför du avbildningen till Docker Hub med kommandot [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

När push-överföringen lyckas kan du använda avbildningen som distributions källa för en ny function-app i Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>Skapa en Premium-plan

Linux-värd för anpassade funktions behållare som stöds på [dedikerade (App Service) planer](functions-scale.md#app-service-plan) och [Premium-planer](functions-premium-plan.md#features). I den här självstudien används en Premium-plan som kan skalas efter behov. Mer information om värdfunktioner finns i [Azure Functions hosting plans comparison](functions-scale.md) (Jämförelse av Azure Functions-värdplaner).

I följande exempel skapas en Premium-plan med namnet `myPremiumPlan` i pris nivån **elastisk Premium 1** (`--sku EP1`) i regionen USA, västra (`-location WestUS`) och i en Linux-behållare (`--is-linux`).

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-an-app-from-the-image"></a>Skapa en app från avbildningen

Function-appen hanterar körningen av dina funktioner i värd planen. Skapa en funktionsapp från en Docker Hub-avbildning med kommandot [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

I följande kommando infogar du ett unikt funktionsappnamn istället för platshållaren `<app_name>` och lagringskontonamnet istället för `<storage_name>`. `<app_name>` används som DNS-standarddomän för funktionsappen. Därför måste namnet vara unikt bland alla appar i Azure. Precis som tidigare är `<docker-id>` ditt Docker-kontonamn.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myPremiumPlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

Parametern _deployment-container-image-name_ anger vilken avbildning på Docker Hub som ska användas för att skapa funktionsappen. Använd kommandot [AZ functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) för att visa information om den avbildning som används för distribution. Använd kommandot [AZ functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) för att distribuera från en annan avbildning.

## <a name="configure-the-function-app"></a>Konfigurera funktionsappen

Funktionen behöver anslutningssträngen för att ansluta till standardlagringskontot. När du publicerar en anpassad avbildning till ett privat behållar konto bör du i stället ange dessa program inställningar som miljövariabler i Dockerfile med hjälp av en [miljö instruktion](https://docs.docker.com/engine/reference/builder/#env), eller något liknande.

I det här fallet är `<storage_name>` namnet på det lagringskonto du skapade. Visa anslutningssträngen med kommandot [az storage account show-connection-string](/cli/azure/storage/account). Lägg till dessa programinställningar i funktionsappen med kommandot [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_name> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <app_name> \
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

## <a name="verify-your-functions"></a>Verifiera dina funktioner

<!-- we should replace this with a CLI or API-based approach, when we get something better than REST -->

Den HTTP-utlöst funktion som du skapade kräver en [funktions nyckel](functions-bindings-http-webhook.md#authorization-keys) när du anropar slut punkten. För närvarande är det enklaste sättet att hämta funktions webb adressen, inklusive nyckeln, från [Azure Portal]. 

> [!TIP]
> Du kan också hämta funktions nycklar med hjälp av [API: er för nyckel hantering](https://github.com/Azure/azure-functions-host/wiki/Key-management-API), vilket kräver att du presenterar en [Bearer-token för autentisering](/cli/azure/account#az-account-get-access-token).

Leta upp din nya Function-app i [Azure Portal] genom att skriva in namnet på din app i rutan **Sök** högst upp på sidan och välja **App Service** resurs.

Välj funktionen **MyHttpTrigger** , Välj **</> Hämta funktions webb adress** > **standard (funktions nyckel)**  > **kopia**.

![Kopiera funktionswebbadressen från Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)

I den här URL: en är funktions nyckeln parametern `code`. 

> [!NOTE]  
> Eftersom din Function-app distribueras som en behållare kan du inte göra ändringar i funktions koden i portalen. Du måste i stället uppdatera projektet i en lokal behållare och publicera det på Azure igen.

Klistra in funktionens URL i adressfältet för din webbläsare. Lägg till frågesträngvärdet `&name=<yourname>` i slutet av den här webbadressen och tryck på knappen `Enter` på tangentbordet för att utföra begäran. Du bör se svaret som returnerades av funktionen som visas i webbläsaren.

I följande exempel visas svaret i webbläsaren:

![Funktionssvar i webbläsaren.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

Begäransadressen innehåller en nyckel som krävs för åtkomst till din funktion över HTTP. 

## <a name="enable-continuous-deployment"></a>Aktivera kontinuerlig distribution

En av fördelarna med att använda behållare är stöd för kontinuerlig distribution. Med funktioner kan du automatiskt distribuera uppdateringar när din behållare uppdateras i registret. Aktivera kontinuerlig distribution med kommandot [AZ functionapp Deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) .

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

Det här kommandot returnerar distributions-webhook-URL: en när kontinuerlig distribution har Aktiver ATS. Du kan också använda kommandot [AZ functionapp Deployment container show-CD-URL](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) för att returnera denna URL. 

Kopiera distributions-URL: en och bläddra till din DockerHub-lagrings platsen, Välj fliken **Webhooks** , ange ett webhook- **namn** för webhooken, klistra in webb adressen i **webhook-URL**: en och välj sedan plus tecknet ( **+** ).

![Lägg till webhooken i din DockerHub-lagrings platsen](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

Med webhooken kan alla uppdateringar av den länkade avbildningen i DockerHub resultera i att appen hämtar och installerar den senaste avbildningen.

## <a name="enable-ssh-connections"></a>Aktivera SSH-anslutningar

SSH möjliggör säker kommunikation mellan en container och en klient. Med SSH aktiverat kan du ansluta till din behållare med hjälp av App Service Advanced tools (kudu). För att göra det enkelt att ansluta till din behållare med hjälp av SSH tillhandahåller funktioner en bas avbildning som har SSH redan aktiverat. 

### <a name="change-the-base-image"></a>Ändra bas avbildningen

I din Dockerfile lägger du till strängen `-appservice` till bas avbildningen i din `FROM`-instruktion, som för ett JavaScript-projekt ser ut ungefär så här.

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
```

Skillnaderna i de två bas avbildningarna möjliggör SSH-anslutningar till din behållare. Dessa skillnader beskrivs i [den här app Services själv studie kursen](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

### <a name="rebuild-and-redeploy-the-image"></a>Återskapa och distribuera om avbildningen

I rotmappen kör du kommandot [Docker build](https://docs.docker.com/engine/reference/commandline/build/) igen, som före, ersätter `<docker-id>` med ditt konto-ID för Docker Hub. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Skicka tillbaka den uppdaterade avbildningen till Docker Hub.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Den uppdaterade avbildningen distribueras om till Function-appen.

### <a name="connect-to-your-container-in-azure"></a>Anslut till din behållare i Azure

I webbläsaren navigerar du till följande avancerade verktyg (kudu) `scm.`-slutpunkt för din Function app-behållare, ersätter `<app_name>` med namnet på din Function-app.

```
https://<app_name>.scm.azurewebsites.net/
```

Logga in på ditt Azure-konto och välj sedan fliken **SSH** för att skapa en SSH-anslutning till din behållare.

När anslutningen har upprättats kör du kommandot `top` för att visa de processer som körs. 

![Linux Top-kommandot körs i en SSH-session.](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-queue-storage"></a>Skriv till Queue Storage

Med Functions kan du ansluta Azure-tjänster och andra resurser till funktioner utan att behöva skriva din egen integrerings kod. Dessa *bindningar*, som representerar både indata och utdata, deklareras i funktions definitionen. Data från bindningar tillhandahålls till funktionen som parametrar. En *utlösare* är en särskild typ av ingående bindning. Även om en funktion bara har en utlösare, kan den ha flera indata och utdata-bindningar. Mer information finns i [Azure Functions utlösare och bindningar begrepp](functions-triggers-bindings.md).

I det här avsnittet visas hur du integrerar din funktion med en Azure Storage kö. Den utgående bindning som du lägger till i den här funktionen skriver data från en HTTP-begäran till ett meddelande i kön.

### <a name="download-the-function-app-settings"></a>Ladda ned appens funktions inställningar

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

### <a name="enable-extension-bundles"></a>Aktivera tilläggs paket

Eftersom du använder en kö för lagring av utdata måste du ha installerat tillägget för lagrings bindningar innan du kör projektet. 


# <a name="javascript--pythontabnodejspython"></a>[Java Script/python](#tab/nodejs+python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

Med undantag för HTTP-och timer-utlösare implementeras bindningar som tilläggs paket. Kör följande [dotNet Lägg till paket](/dotnet/core/tools/dotnet-add-package) kommando i terminalfönstret för att lägga till lagrings tilläggs paketet i projektet.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

> [!TIP]
> När du använder Visual Studio kan du också använda NuGet Package Manager för att lägga till det här paketet.

---

Nu kan du lägga till en bindning för lagring av utdata till ditt projekt.

### <a name="add-an-output-binding"></a>Lägg till en utdatabindning

I funktioner kräver varje typ av bindning en `direction`, `type` och en unik `name` som ska definieras i function. JSON-filen. Hur du definierar dessa attribut beror på språket i din Function-app.

# <a name="javascript--pythontabnodejspython"></a>[Java Script/python](#tab/nodejs+python)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

### <a name="add-code-that-uses-the-output-binding"></a>Lägg till kod som använder utdatabindning

När bindningen har definierats kan du använda `name` för bindningen för att komma åt den som ett attribut i funktions under skriften. Genom att använda en utgående bindning behöver du inte använda den Azure Storage SDK-koden för autentisering, hämta en Queue referens eller skriva data. Bindningarna Functions Runtime och Queue output utför dessa uppgifter åt dig.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

### <a name="update-the-hosted-container"></a>Uppdatera den värdbaserade behållaren

I rotmappen kör du [Docker build](https://docs.docker.com/engine/reference/commandline/build/) -kommandot igen och den här gången uppdaterar du versionen i taggen till `v1.0.2`. Som tidigare ersätter du `<docker-id>` med ditt konto-ID för Docker Hub. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Skicka tillbaka den uppdaterade avbildningen till lagrings platsen.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

### <a name="verify-the-updates-in-azure"></a>Verifiera uppdateringarna i Azure

Använd samma URL som tidigare från webbläsaren för att utlösa din funktion. Du bör se samma svar. Men den här gången är strängen som du skickar som parametern `name` skriven i lagrings kön `outqueue`.

### <a name="set-the-storage-account-connection"></a>Ange lagrings konto anslutningen

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

### <a name="query-the-storage-queue"></a>Fråga lagrings kön

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat din anpassade behållare till en Function-app i Azure kan du läsa mer om följande ämnen:

+ [Övervaknings funktioner](functions-monitoring.md)
+ [Skalnings-och värd alternativ](functions-scale.md)
+ [Kubernetes-baserad server lös värd](functions-kubernetes-keda.md)

[Azure Portal]: https://portal.azure.com
