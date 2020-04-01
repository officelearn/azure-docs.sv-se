---
title: Skapa Azure-funktioner på Linux med hjälp av en anpassad avbildning
description: Lär dig hur du skapar en Azure Functions som körs på en anpassad Linux-avbildning.
ms.date: 01/15/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 8c074c677c645dd03e3cf5288d82aa3e65720e8b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239632"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Skapa en funktion på Linux med en anpassad behållare

I den här självstudien skapar och distribuerar du Python-kod till Azure Functions som en anpassad Docker-behållare med hjälp av en Linux-basavbildning. Du använder vanligtvis en anpassad avbildning när dina funktioner kräver en viss språkversion eller har ett specifikt beroende eller en specifik konfiguration som inte tillhandahålls av den inbyggda avbildningen.

Du kan också använda en standardbehållare för Azure App Service enligt beskrivningen på [Skapa din första funktion som finns på Linux](functions-create-first-azure-function-azure-cli-linux.md). Basavbildningar som stöds för Azure Functions finns i [azure functions basavbildningar repo](https://hub.docker.com/_/microsoft-azure-functions-base).

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en funktionsapp och Dockerfile med hjälp av Azure Functions Core Tools.
> * skapa en anpassad avbildning med Docker
> * Publicera en anpassad avbildning till ett containerregister.
> * Skapa stödresurser i Azure för funktionsappen
> * distribuera en funktionsapp från Docker Hub
> * lägga till programinställningar i funktionsappen.
> * Aktivera kontinuerlig distribution.
> * Aktivera SSH-anslutningar till behållaren.
> * Lägg till en kölagringsutdatabindning. 

Du kan följa den här självstudien på alla datorer som kör Windows, Mac OS eller Linux. Om du slutför självstudien kostar det några us-dollar i ditt Azure-konto.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.7.1846 eller senare
- [Azure CLI](/cli/azure/install-azure-cli) version 2.0.77 eller senare
- [Azure Functions 2.x-körningen](functions-versions.md)
- Följande språkkörningskomponenter:
    ::: zone pivot="programming-language-csharp"
    - [.NET Core 2.2.x eller senare](https://dotnet.microsoft.com/download)
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - [Node.js](https://nodejs.org/en/download/)
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - [PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7)
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - [Python 3.6 - 64 bitar](https://www.python.org/downloads/release/python-3610/) eller [Python 3.7 - 64 bitar](https://www.python.org/downloads/release/python-376/)
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - [Node.js](https://nodejs.org/en/download/)
    - [TypeScript](http://www.typescriptlang.org/#download-links)
    ::: zone-end
- [Docker](https://docs.docker.com/install/)
- Ett [Docker-ID](https://hub.docker.com/signup)

### <a name="prerequisite-check"></a>Förutsättningskontroll

1. I ett terminal- eller `func --version` kommandofönster kör du för att kontrollera att Azure Functions Core Tools är version 2.7.1846 eller senare.
1. Kör `az --version` för att kontrollera att Azure CLI-versionen är 2.0.76 eller senare.
1. Kör `az login` för att logga in på Azure och verifiera en aktiv prenumeration.
1. Kör `docker login` för att logga in på Docker. Det här kommandot misslyckas om Docker inte körs, i vilket fall starta docker och försök igen kommandot.

## <a name="create-and-test-the-local-functions-project"></a>Skapa och testa projektet för lokala funktioner

1. I en terminal eller kommandotolk skapar du en mapp för den här självstudien på en lämplig plats och navigerar sedan till den mappen.

1. Följ instruktionerna på [Skapa och aktivera en virtuell miljö](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#create-venv) för att skapa en virtuell miljö som kan användas med den här självstudien.

1. Kör följande kommando för det språk du valt om `LocalFunctionsProject`du vill skapa ett funktionsappprojekt i en mapp med namnet . Alternativet `--docker` genererar en `Dockerfile` för projektet, som definierar en lämplig anpassad behållare för användning med Azure Functions och den valda körningen.

    ::: zone pivot="programming-language-csharp"
    ```
    func init LocalFunctionsProject --worker-runtime dotnet --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language javascript --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func init LocalFunctionsProject --worker-runtime powershell --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func init LocalFunctionsProject --worker-runtime python --docker
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```
    func init LocalFunctionsProject --worker-runtime node --language typescript --docker
    ```
    ::: zone-end
    
1. Navigera till projektmappen:

    ```
    cd LocalFunctionsProject
    ```
    
1. Lägg till en funktion i projektet med `--name` hjälp av följande kommando, där `--template` argumentet är det unika namnet på din funktion och argumentet anger funktionens utlösare. `func new`skapa en undermapp som matchar funktionsnamnet som innehåller en kodfil som är lämplig för projektets valda språk och en konfigurationsfil med namnet *function.json*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

1. Om du vill testa funktionen lokalt startar du den lokala Azure Functions-körningsvärden i mappen *LocalFunctionsProject:*
   
    ::: zone pivot="programming-language-csharp"
    ```
    func start --build
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```
    func start
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```
    func start
    ```
    ::: zone-end    

    ::: zone pivot="programming-language-typescript"
    ```
    npm install
    ```

    ```
    npm start
    ```
    ::: zone-end

1. När du `HttpExample` ser slutpunkten visas i `http://localhost:7071/api/HttpExample?name=Functions`utdata navigerar du till . Webbläsaren ska visa ett meddelande som "Hello, Functions" (varierat något beroende på ditt valda programmeringsspråk).

1. Använd **Ctrl**-**C** för att stoppa värden.

## <a name="build-the-container-image-and-test-locally"></a>Skapa behållaravbildningen och testa lokalt

1. (Valfritt) Undersök *Dockerfile" i mappen *LocalFunctionsProj.* Dockerfile beskriver den miljö som krävs för att köra funktionsappen på Linux: 

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk AS installer-env

    COPY . /src/dotnet-function-app
    RUN cd /src/dotnet-function-app && \
        mkdir -p /home/site/wwwroot && \
        dotnet publish *.csproj --output /home/site/wwwroot
    
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/dotnet:2.0-appservice 
    FROM mcr.microsoft.com/azure-functions/dotnet:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY --from=installer-env ["/home/site/wwwroot", "/home/site/wwwroot"]
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/powershell:2.0
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY requirements.txt /
    RUN pip install -r /requirements.txt
    
    COPY . /home/site/wwwroot    
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    # To enable ssh & remote debugging on app service change the base image to the one below
    # FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    FROM mcr.microsoft.com/azure-functions/node:2.0
    
    ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
        AzureFunctionsJobHost__Logging__Console__IsEnabled=true
    
    COPY . /home/site/wwwroot
    
    RUN cd /home/site/wwwroot && \
    npm install    
    ```
    ::: zone-end

    > [!NOTE]
    > Den fullständiga listan över basavbildningar som stöds för Azure Functions finns på bassidan för [Azure Functions.](https://hub.docker.com/_/microsoft-azure-functions-base)
    
1. I mappen *LocalFunctionsProject* kör du [kommandot docker build](https://docs.docker.com/engine/reference/commandline/build/) `azurefunctionsimage`och anger `v1.0.0`ett namn, och en tagg, . Ersätt `<docker_id>` med ditt konto-ID för Docker Hub. Det här kommandot skapar Docker-avbildningen för containern.

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
    När kommandot är klart kan du köra den nya behållaren lokalt.
    
1. Om du vill testa versionen kör du avbildningen i en `<docker_id>` lokal behållare med kommandot [docker run, ersätter](https://docs.docker.com/engine/reference/commandline/run/) igen med Docker-ID:et och lägger till portarargumentet: `-p 8080:80`

    ```
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. När bilden körs i en lokal behållare `http://localhost:8080`öppnar du en webbläsare för , som ska visa platshållarbilden som visas nedan. Avbildningen visas nu eftersom din funktion körs i den lokala behållaren, som den skulle i Azure, vilket innebär att `"authLevel": "function"` den skyddas av en åtkomstnyckel enligt definitionen i *function.json* med egenskapen. Behållaren har ännu inte publicerats till en funktionsapp i Azure, så nyckeln är ännu inte tillgänglig. Om du vill testa lokalt, stoppa docker, `"authLevel": "anonymous"`ändra auktoriseringsegenskapen till , återskapa avbildningen och starta om docker. Återställ `"authLevel": "function"` sedan i *function.json*. Mer information finns i [auktoriseringsnycklar](functions-bindings-http-webhook-trigger.md#authorization-keys).

    ![Platshållarbild som anger att behållaren körs lokalt](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

1. När du har verifierat funktionsappen i behållaren stoppar du dockern med **Ctrl**+**C**.

## <a name="push-the-image-to-docker-hub"></a>Skicka bilden till Docker Hub

Docker Hub är ett behållarregister som är värd för avbildningar och tillhandahåller avbildnings- och behållartjänster. Om du vill dela avbildningen, som inkluderar distribution till Azure, måste du skicka den till ett register.

1. Om du inte redan har loggat in på Docker gör `<docker_id>` du det med [inloggningskommandot docker](https://docs.docker.com/engine/reference/commandline/login/) och ersätter med Docker-ID:t. Detta kommando frågar dig för ditt användarnamn och lösenord. Meddelandet "Inloggning lyckades" bekräftar att du är inloggad.

    ```
    docker login
    ```
    
1. När du har loggat in skickar du avbildningen till Docker `<docker_id>` Hub med hjälp av [docker-push-kommandot](https://docs.docker.com/engine/reference/commandline/push/) och ersätter igen med Docker-ID:t.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. Beroende på nätverkshastigheten kan det ta några minuter att trycka på bilden första gången (det går mycket snabbare att trycka på den efterföljande datorn). Medan du väntar kan du gå vidare till nästa avsnitt och skapa Azure-resurser i en annan terminal.

## <a name="create-supporting-azure-resources-for-your-function"></a>Skapa stöd för Azure-resurser för din funktion

Om du vill distribuera funktionskoden till Azure måste du skapa tre resurser:

- En resursgrupp, som är en logisk behållare för relaterade resurser.
- Ett Azure Storage-konto som upprätthåller tillstånd och annan information om dina projekt.
- En Azure-funktionsapp som tillhandahåller miljön för att köra din funktionskod. En funktionsapp mappas till ditt lokala funktionsprojekt och låter dig gruppera funktioner som en logisk enhet för enklare hantering, distribution och delning av resurser.

Du använder Azure CLI-kommandon för att skapa dessa objekt. Varje kommando ger JSON-utdata när det är klart.

1. Logga in på Azure med kommandot [az login:](/cli/azure/reference-index#az-login)

    ```azurecli
    az login
    ```
    
1. Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). I följande exempel skapas `AzureFunctionsContainers-rg` en `westeurope` resursgrupp med namnet i regionen. (Du skapar vanligtvis resursgruppen och resurserna i en region nära `az account list-locations` dig med hjälp av en tillgänglig region från kommandot.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Du kan inte vara värd för Linux- och Windows-appar i samma resursgrupp. Om du har en `AzureFunctionsContainers-rg` befintlig resursgrupp med namnet med en Windows-funktionsapp eller webbapp måste du använda en annan resursgrupp.
    
1. Skapa ett allmänt lagringskonto i resursgruppen och regionen med kommandot [skapa az-lagringskonto.](/cli/azure/storage/account#az-storage-account-create) I följande exempel `<storage_name>` ersätter du med ett globalt unikt namn som passar dig. Namnen får innehålla endast tre till 24 teckens siffror och gemener. `Standard_LRS`anger ett typiskt konto för allmänt ändamål.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Lagringskontot ådrar sig bara några USD cent för den här guiden.
    
1. Använd kommandot för att skapa en `myPremiumPlan` Premium-plan för Azure-funktioner`--sku EP1`som namnges i`-location westeurope`prisnivån Elastisk **Premium 1** ( ),`--is-linux`i regionen Västeuropa ( eller använd en lämplig region nära dig) och i en Linux-behållare ( ).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Linux hosting för anpassade funktioner behållare stöds på [Dedikerade (App Service) planer](functions-scale.md#app-service-plan) och [Premium planer](functions-premium-plan.md#features). Vi använder Premium-planen här, som kan skala efter behov. Mer information om värdfunktioner finns i [Azure Functions hosting plans comparison](functions-scale.md) (Jämförelse av Azure Functions-värdplaner). Om du vill beräkna kostnader finns på [sidan Funktioners priser](https://azure.microsoft.com/pricing/details/functions/).

    Kommandot etablerar också en associerad Azure Application Insights-instans i samma resursgrupp, med vilken du kan övervaka din funktionsapp och visa loggar. Mer information finns i [Övervaka Azure-funktioner](functions-monitoring.md). Instansen medför inga kostnader förrän du aktiverar den.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Skapa och konfigurera en funktionsapp på Azure med avbildningen

En funktionsapp på Azure hanterar körningen av dina funktioner i din värdplan. I det här avsnittet använder du Azure-resurserna från föregående avsnitt för att skapa en funktionsapp från en avbildning på Docker Hub och konfigurera den med en anslutningssträng till Azure Storage.

1. Skapa appen Funktioner med kommandot [az functionapp create.](/cli/azure/functionapp#az-functionapp-create) I följande exempel `<storage_name>` ersätter du med det namn som du använde i föregående avsnitt för lagringskontot. Ersätt `<app_name>` också med ett globalt unikt namn `<docker_id>` som passar dig och med Docker-ID:n.

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    Parametern *deployment-container-image-name* anger vilken avbildning som ska användas för funktionsappen. Du kan använda kommandot [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) för att visa information om avbildningen som används för distribution. Du kan också använda kommandot [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) för att distribuera från en annan avbildning.

1. Hämta anslutningssträngen för lagringskontot som du skapade med kommandot az storage account `storageConnectionString` [show-connection-string](/cli/azure/storage/account) och tilldela den till en skalvariabel:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Lägg till den här inställningen i funktionsappen med kommandot [az functionapp config appsettings](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) set. I följande kommando `<app_name>` ersätter du med namnet på `<connection_string>` funktionsappen och ersätter med anslutningssträngen från föregående steg (en lång kodad sträng som börjar med "DefaultEndpointProtocol="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

1. Funktionen kan nu använda den här anslutningssträngen för att komma åt lagringskontot.

> [!TIP]
> I bash kan du använda en skalvariabel för att fånga anslutningssträngen i stället för att använda Urklipp. Använd först följande kommando för att skapa en variabel med anslutningssträngen:
> 
> ```bash
> storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
> ```
> 
> Se sedan variabeln i det andra kommandot:
> 
> ```azurecli
> az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
> ```

> [!NOTE]    
> Om du publicerar den anpassade avbildningen på ett privat behållarkonto bör du använda miljövariabler i Dockerfile för anslutningssträngen i stället. Mer information finns i [ENV-instruktionen](https://docs.docker.com/engine/reference/builder/#env). Du bör också ställa `DOCKER_REGISTRY_SERVER_USERNAME` `DOCKER_REGISTRY_SERVER_PASSWORD`in variablerna och . Om du vill använda värdena måste du sedan återskapa avbildningen, skicka avbildningen till registret och sedan starta om funktionsappen på Azure.

## <a name="verify-your-functions-on-azure"></a>Verifiera dina funktioner på Azure

Med avbildningen distribuerad till funktionsappen på Azure kan du nu anropa funktionen via HTTP-begäranden. Eftersom *function.json-definitionen* innehåller `"authLevel": "function"`egenskapen måste du först hämta åtkomstnyckeln (kallas även "funktionsnyckeln") och inkludera den som en URL-parameter i alla begäranden till slutpunkten.

1. Hämta funktions-URL:en med åtkomstnyckeln (funktion) med hjälp av `az rest` Azure-portalen eller genom att använda Azure CLI med kommandot.)

    # <a name="portal"></a>[Portalen](#tab/portal)

    1. Logga in på Azure-portalen och leta sedan reda på din funktionsapp genom att ange namnet på din funktionsapp i **sökrutan** högst upp på sidan. I resultatet väljer du **App Service-resursen.**

    1. Välj namnet på funktionen under **Funktioner (Skrivskyddade)** på den vänstra navigeringspanelen.

    1. Välj **</> Hämta funktions-URL**på informationspanelen:
    
        ![Kommandot Hämta funktions-URL på Azure-portalen](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. I popup-fönstret väljer du **standard (funktionstangent)** och **kopierar**sedan . Nyckeln är teckensträngen `?code=`som följer .

        ![Kopiera funktions-URL:en från Azure-portalen](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > Eftersom din funktionsapp distribueras som en behållare kan du inte göra ändringar i funktionskoden i portalen. Du måste i stället uppdatera projektet i den lokala avbildningen, skicka avbildningen till registret igen och sedan distribuera om till Azure. Du kan ställa in kontinuerlig distribution i ett senare avsnitt.
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

    1. Skapa en URL-sträng i `<subscription_id>`följande `<resource_group>`format `<app_name>` och ersätt , och med ditt Azure-prenumerations-ID, resursgruppen för din funktionsapp respektive namnet på din funktionsapp:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Webbadressen kan till exempel se ut som följande adress:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > För enkelhetens skull kan du i stället tilldela `az rest` url:en till en miljövariabel och använda den i kommandot.
    
    1. Kör följande `az rest` kommando (tillgängligt i Azure CLI version 2.0.77 och senare) och ersätt med `<uri>` URI-strängen från det sista steget, inklusive citattecken:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. Utdata från kommandot är funktionsnyckeln. Den fullständiga `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`funktions-URL:en ersätter `<app_name>`sedan , `<function_name>`och `<key>` med dina specifika värden.
    
        > [!NOTE]
        > Nyckeln som hämtas här är *värdnyckeln* som fungerar för alla funktioner i funktionsappen. den metod som visas för portalen hämtar bara nyckeln för en funktion.

    ---

1. Klistra in funktions-URL:en i webbläsarens `&name=Azure` adressfält och lägg till parametern i slutet av webbadressen. Text som "Hello Azure" ska visas i webbläsaren.

    ![Funktionssvar i webbläsaren.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Om du vill testa auktoriseringen tar du bort parametern code= från URL:en och kontrollerar att du inte får något svar från funktionen.


## <a name="enable-continuous-deployment-to-azure"></a>Aktivera kontinuerlig distribution till Azure

Du kan aktivera Azure Functions för att automatiskt uppdatera distributionen av en avbildning när du uppdaterar avbildningen i registret.

1. Aktivera kontinuerlig distribution med kommandot [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) och ersätt med `<app_name>` namnet på funktionsappen:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Det här kommandot möjliggör kontinuerlig distribution och returnerar webbkroks-URL:en för distribution. (Du kan hämta den här URL:en när som helst med kommandot [az functionapp deployment container show-cd-url.)](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url)

1. Kopiera url:en för distributionswebbkrok till Urklipp.

1. Öppna [Docker Hub,](https://hub.docker.com/)logga in och välj **Databaser** i navigeringsfältet. Leta reda på och markera bild, välj fliken **Webhooks,** ange ett **Webhook-namn,** klistra in webbadressen i **Webhook-URL:en**och välj sedan **Skapa:**

    ![Lägg till webhooken i repoeten i DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Med webhook-uppsättningen distribuerar Azure Functions om avbildningen när du uppdaterar den i Docker Hub.

## <a name="enable-ssh-connections"></a>Aktivera SSH-anslutningar

SSH möjliggör säker kommunikation mellan en container och en klient. Med SSH aktiverat kan du ansluta till din behållare med Hjälp av Kudu (App Service Advanced Tools). Azure Functions tillhandahåller en basavbildning som redan har aktiverat SSH för att göra det enkelt att ansluta till din behållare med SSH. Du behöver bara redigera Dockerfile och sedan återskapa och distribuera om avbildningen. Du kan sedan ansluta till behållaren via De avancerade verktygen (Kudu)

1. Lägg till strängen `-appservice` i basbilden i `FROM` instruktionen i Dockerfile:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM microsoft/dotnet:2.2-sdk-appservice AS installer-env
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    Skillnaderna mellan basavbildningarna beskrivs i [självstudiekursen App Services - Custom docker-avbildningar](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

1. Återskapa avbildningen `docker build` med kommandot `<docker_id>` igen och ersätt med Docker-ID:Rebuild the image by using the command again, replacing with your Docker ID:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Skicka den uppdaterade bilden till Docker Hub, vilket bör ta betydligt kortare tid än den första push endast de uppdaterade segmenten av bilden behöver laddas upp.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions distribuerar automatiskt om avbildningen till din funktionsapp. processen sker på mindre än en minut.

1. Öppna `https://<app_name>.scm.azurewebsites.net/`i en webbläsare `<app_name>` och ersätt med ditt unika namn. Den här URL:en är slutpunkten avancerade verktyg (Kudu) för din funktionsappbehållare.

1. Logga in på ditt Azure-konto och välj sedan **SSH** för att upprätta en anslutning till behållaren. Det kan ta en stund att ansluta om Azure fortfarande håller på att uppdatera behållaravbildningen.

1. När en anslutning har upprättats med `top` behållaren kör du kommandot för att visa de processer som körs. 

    ![Linux-toppkommando som körs i en SSH-session](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Skriv till en Azure Storage-kö

Med Azure Functions kan du ansluta dina funktioner till andra Azure-tjänster och resurser som måste skriva din egen integrationskod. Dessa *bindningar*, som representerar både indata och utdata, deklareras i funktionsdefinitionen. Data från bindningar anges för funktionen som parametrar. En *utlösare* är en speciell typ av indatabindning. Även om en funktion bara har en utlösare kan den ha flera in- och utdatabindningar. Mer information finns i [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)begrepp .

Det här avsnittet visar hur du integrerar din funktion med en Azure Storage-kö. Utdatabindningen som du lägger till i den här funktionen skriver data från en HTTP-begäran till ett meddelande i kön.

## <a name="retrieve-the-azure-storage-connection-string"></a>Hämta anslutningssträngen för Azure Storage

Tidigare har du skapat ett Azure Storage-konto som ska användas av funktionsappen. Anslutningssträngen för det här kontot lagras säkert i appinställningar i Azure. Genom att hämta inställningen till filen *local.settings.json* kan du använda den anslutningsskrivningen till en lagringskö i samma konto när du kör funktionen lokalt. 

1. Från projektets rot kör du följande `<app_name>` kommando och ersätter med namnet på funktionsappen från föregående snabbstart. Det här kommandot skriver över alla befintliga värden i filen.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Öppna *local.settings.json* och leta `AzureWebJobsStorage`reda på värdet med namnet , som är anslutningssträngen för lagringskonto. Du använder `AzureWebJobsStorage` namnet och anslutningssträngen i andra avsnitt i den här artikeln.

> [!IMPORTANT]
> Eftersom *local.settings.json* innehåller hemligheter som hämtats från Azure, utesluter du alltid den här filen från källkontrollen. *Gitignore-filen* som skapats med ett lokalt funktionsprojekt utesluter filen som standard.

### <a name="add-an-output-binding-to-functionjson"></a>Lägga till en utdatabindning i function.json

I Azure Functions kräver varje `direction`typ `type`av bindning en , och en unik `name` som ska definieras i filen *function.json.* Din *function.json* innehåller redan en indatabindning för typen "httpTrigger" och en utdatabindning för HTTP-svaret. Om du vill lägga till en bindning i en lagringskö ändrar du filen på följande sätt, som lägger till en utdatabindning för typen "kö", där kön visas i koden som ett indataargument med namnet `msg`. Köbindningen kräver också namnet på den kö `outqueue`som ska användas, i det här fallet `AzureWebJobStorage`, och namnet på de inställningar som innehåller anslutningssträngen, i det här fallet .

::: zone pivot="programming-language-csharp"

I ett C#-klassbiblioteksprojekt definieras bindningarna som bindningsattribut på funktionsmetoden. *Filen function.json* skapas sedan automatiskt baserat på dessa attribut.

1. För köbindningen kör du följande [dotnet add-paketkommando](/dotnet/core/tools/dotnet-add-package) för att lägga till lagringstilläggspaketet i projektet.

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
    ```

1. Öppna *HttpTrigger.cs-filen* och lägg till `using` följande uttryck:

    ```cs
    using Microsoft.Azure.WebJobs.Extensions.Storage;
    ```
    
1. Lägg till följande `Run` parameter i metoddefinitionen:
    
    ```csharp
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
    ```
    
    Metoddefinitionen `Run` ska nu matcha följande kod:
    
    ```csharp
    [FunctionName("HttpTrigger")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
        [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
    ```

Parametern `msg` är `ICollector<T>` en typ som representerar en samling meddelanden som skrivs till en utdatabindning när funktionen är klar. I det här fallet är utdata en lagringskö med namnet `outqueue`. Anslutningssträngen för lagringskontot `StorageAccountAttribute`anges av . Det här attributet anger den inställning som innehåller anslutningssträngen för lagringskonto och kan användas på klass-, metod- eller parameternivå. I det här fallet `StorageAccountAttribute` kan du utelämna eftersom du redan använder standardlagringskontot.

::: zone-end

::: zone pivot="programming-language-javascript"

Uppdatera *function.json* så att följande matchas genom att lägga till köbindningen efter HTTP-bindningen:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-powershell"

Uppdatera *function.json* så att följande matchas genom att lägga till köbindningen efter HTTP-bindningen:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-python"

Uppdatera *function.json* så att följande matchas genom att lägga till köbindningen efter HTTP-bindningen:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

::: zone pivot="programming-language-typescript"

Uppdatera *function.json* så att följande matchas genom att lägga till köbindningen efter HTTP-bindningen:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```
::: zone-end

## <a name="add-code-to-use-the-output-binding"></a>Lägga till kod för att använda utdatabindningen

När bindningen har definierats visas namnet `msg`på bindningen i det här fallet `context` i funktionskoden som ett argument (eller i objektet i JavaScript och TypeScript). Du kan sedan använda variabeln för att skriva meddelanden till kön. Du måste skriva en kod för autentisering, hämta en köreferens eller skriva data. Alla dessa integrationsuppgifter hanteras bekvämt i Azure Functions runtime och köutdatabindning.

::: zone pivot="programming-language-csharp"
```csharp
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a message to the output collection.
        msg.Add(string.Format("Name passed to the function: {0}", name));
    }
    
    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```
::: zone-end

::: zone pivot="programming-language-javascript"
```js
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    if (req.query.name || (req.body && req.body.name)) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);

        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};
```
::: zone-end

::: zone pivot="programming-language-powershell"
```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$name = $Request.Query.Name
if (-not $name) {
    $name = $Request.Body.Name
}

if ($name) {
    $outputMsg = "Name passed to the function: $name"
    Push-OutputBinding -name msg -Value $outputMsg

    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
else {
    $status = [HttpStatusCode]::BadRequest
    $body = "Please pass a name on the query string or in the request body."
}

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```
::: zone-end

::: zone pivot="programming-language-python"
```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```
::: zone-end

::: zone pivot="programming-language-typescript"
```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions"

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    context.log('HTTP trigger function processed a request.');
    const name = (req.query.name || (req.body && req.body.name));

    if (name) {
        // Add a message to the Storage queue.
        context.bindings.msg = "Name passed to the function: " +
            (req.query.name || req.body.name);
        
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
};

export default httpTrigger;
```
::: zone-end

### <a name="update-the-image-in-the-registry"></a>Uppdatera avbildningen i registret

1. I rotmappen `docker build` kör du igen och den här `v1.0.1`gången uppdaterar du versionen i taggen till . Som tidigare, `<docker_id>` ersätt med ditt Docker Hub-konto-ID:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. Skicka tillbaka den uppdaterade bilden `docker push`till databasen med:

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Eftersom du har konfigurerat kontinuerlig leverans uppdateras automatiskt din funktionsapp i Azure genom att uppdatera avbildningen i registret igen.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Visa meddelandet i Azure Storage-kön

I en webbläsare använder du samma WEBBADRESS som tidigare för att anropa din funktion. Webbläsaren ska visa samma svar som tidigare, eftersom du inte har ändrat den delen av funktionskoden. Den tillagda koden skrev dock `name` ett meddelande `outqueue` med url-parametern i lagringskön.

Du kan visa kön i [Azure-portalen](../storage/queues/storage-quickstart-queues-portal.md) eller i [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Du kan också visa kön i Azure CLI enligt beskrivningen i följande steg:

1. Öppna funktionsprojektets *local.setting.json-fil* och kopiera anslutningssträngvärdet. I ett terminal- eller kommandofönster kör du följande `AZURE_STORAGE_CONNECTION_STRING`kommando för att skapa en `<connection_string>`miljövariabel med namnet , klistra in din specifika anslutningssträng i stället för . (Den här miljövariabeln innebär att du inte behöver ange `--connection-string` anslutningssträngen till varje efterföljande kommando med argumentet.)

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. (Valfritt) Använd [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) kommandot för att visa lagringsköerna i ditt konto. Utdata från det här kommandot `outqueue`ska innehålla en kö med namnet , som skapades när funktionen skrev sitt första meddelande till den kön.
    
    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    ---

1. Använd [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) kommandot för att visa meddelandena i den här kön, vilket ska vara det förnamn som du använde när du testade funktionen tidigare. Kommandot hämtar det första meddelandet i kön i [base64-kodningen,](functions-bindings-storage-queue-trigger.md#encoding)så du måste också avkoda meddelandet så att det visas som text.

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    Eftersom du måste avreferna meddelandesamlingen och avkoda från base64 kör du PowerShell och använder kommandot PowerShell.

    ---

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta arbeta med Azure-funktionen med hjälp av de resurser som du skapade i den här självstudien kan du lämna alla dessa resurser på plats. Eftersom du har skapat en Premium-plan för Azure Functions ådrar du dig en eller två USD per dag i löpande kostnader.

Om du vill undvika `AzureFunctionsContainer-rg` löpande kostnader tar du bort resursgruppen för att rensa alla resurser i den gruppen: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Nästa steg

+ [Övervakningsfunktioner](functions-monitoring.md)
+ [Alternativ för skalning och hosting](functions-scale.md)
+ [Kubernetes-baserad serverlös hosting](functions-kubernetes-keda.md)
