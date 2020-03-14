---
title: Skapa Azure Functions på Linux med en anpassad avbildning
description: Lär dig hur du skapar en Azure Functions som körs på en anpassad Linux-avbildning.
ms.date: 01/15/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 8c074c677c645dd03e3cf5288d82aa3e65720e8b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239632"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Skapa en funktion i Linux med en anpassad behållare

I den här självstudien skapar du och distribuerar python-kod till Azure Functions som en anpassad Docker-behållare med en Linux-bas avbildning. Du använder vanligt vis en anpassad bild när funktionerna kräver en specifik språk version eller har ett särskilt beroende eller en specifik konfiguration som inte tillhandahålls av den inbyggda avbildningen.

Du kan också använda en standard-Azure App Service-behållare enligt beskrivningen i [skapa din första funktion på Linux](functions-create-first-azure-function-azure-cli-linux.md). Bas avbildningar som stöds för Azure Functions finns i [Azure Functions Base images-lagrings platsen](https://hub.docker.com/_/microsoft-azure-functions-base).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en Function-app och Dockerfile med hjälp av Azure Functions Core Tools.
> * skapa en anpassad avbildning med Docker
> * Publicera en anpassad avbildning till ett containerregister.
> * Skapa stöd resurser i Azure för Function-appen
> * distribuera en funktionsapp från Docker Hub
> * lägga till programinställningar i funktionsappen.
> * Aktivera kontinuerlig distribution.
> * Aktivera SSH-anslutningar till behållaren.
> * Lägg till en utgående bindning för kö Storage. 

Du kan följa den här självstudien på alla datorer som kör Windows, Mac OS eller Linux. När du har slutfört kursen debiteras du för några amerikanska dollar i ditt Azure-konto.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core tools](./functions-run-local.md#v2) version 2.7.1846 eller senare
- [Azure CLI](/cli/azure/install-azure-cli) -version 2.0.77 eller senare
- [Azure Functions 2. x-körningsmiljön](functions-versions.md)
- Följande språk körnings komponenter:
    ::: zone pivot="programming-language-csharp"
    - [.NET Core 2.2. x eller senare](https://dotnet.microsoft.com/download)
    ::: zone-end
    ::: zone pivot="programming-language-javascript"
    - [Node.js](https://nodejs.org/en/download/)
    ::: zone-end
    ::: zone pivot="programming-language-powershell"
    - [PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7)
    ::: zone-end
    ::: zone pivot="programming-language-python"
    - [Python 3,6-64-bitars](https://www.python.org/downloads/release/python-3610/) eller [Python 3,7-64 bitar](https://www.python.org/downloads/release/python-376/)
    ::: zone-end
    ::: zone pivot="programming-language-typescript"
    - [Node.js](https://nodejs.org/en/download/)
    - [TypeScript](http://www.typescriptlang.org/#download-links)
    ::: zone-end
- [Docker](https://docs.docker.com/install/)
- Ett [Docker-ID](https://hub.docker.com/signup)

### <a name="prerequisite-check"></a>Krav kontroll

1. I ett terminalfönster eller kommando fönster kör `func --version` för att kontrol lera att Azure Functions Core Tools är version 2.7.1846 eller senare.
1. Kör `az --version` för att kontrol lera att Azure CLI-versionen är 2.0.76 eller senare.
1. Kör `az login` för att logga in på Azure och verifiera en aktiv prenumeration.
1. Kör `docker login` för att logga in på Docker. Detta kommando Miss lyckas om Docker inte körs, i vilket fall startar Docker och försöker köra kommandot igen.

## <a name="create-and-test-the-local-functions-project"></a>Skapa och testa det lokala Functions-projektet

1. I en terminal-eller kommando tolk skapar du en mapp för den här självstudien på en lämplig plats och navigerar sedan till mappen.

1. Följ anvisningarna i [skapa och aktivera en virtuell miljö](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#create-venv) för att skapa en virtuell miljö som du kan använda med den här självstudien.

1. Kör följande kommando för ditt valda språk för att skapa ett Function app-projekt i en mapp med namnet `LocalFunctionsProject`. Alternativet `--docker` genererar en `Dockerfile` för projektet, som definierar en lämplig anpassad behållare för användning med Azure Functions och den valda körningen.

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
    
1. Lägg till en funktion i projektet med hjälp av följande kommando, där argumentet `--name` är det unika namnet på din funktion och argumentet `--template` anger funktionens utlösare. `func new` skapa en undermapp som matchar funktions namnet som innehåller en kod fil som är lämplig för projektets valda språk och en konfigurations fil med namnet *Function. JSON*.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

1. Du testar funktionen lokalt genom att starta den lokala Azure Functions körnings värd i mappen *LocalFunctionsProject* :
   
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

1. När du ser `HttpExample`-slutpunkten visas i utdata går du till `http://localhost:7071/api/HttpExample?name=Functions`. Webbläsaren ska visa ett meddelande som "Hello, Functions" (variera något beroende på ditt valda programmeringsspråk).

1. Använd **Ctrl**-**C** för att stoppa värden.

## <a name="build-the-container-image-and-test-locally"></a>Bygg behållar avbildningen och testa lokalt

1. Valfritt Granska * Dockerfile i mappen *LocalFunctionsProj* Dockerfile beskriver den miljö som krävs för att köra Function-appen på Linux: 

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
    > Den fullständiga listan med bas avbildningar som stöds för Azure Functions finns på [sidan för Azure Functions Base-avbildning](https://hub.docker.com/_/microsoft-azure-functions-base).
    
1. I mappen *LocalFunctionsProject* kör du kommandot [Docker build](https://docs.docker.com/engine/reference/commandline/build/) och anger ett namn, `azurefunctionsimage`och tagg `v1.0.0`. Ersätt `<docker_id>` med ditt konto-ID för Docker Hub. Det här kommandot skapar Docker-avbildningen för containern.

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
    När kommandot har slutförts kan du köra den nya behållaren lokalt.
    
1. Testa versionen genom att köra avbildningen i en lokal behållare med kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) , ersätta `<docker_id>` med Docker-ID: t och lägga till port argumentet `-p 8080:80`:

    ```
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. När avbildningen körs i en lokal behållare öppnar du en webbläsare för att `http://localhost:8080`, som ska Visa platshållarbilden som visas nedan. Avbildningen visas nu eftersom din funktion körs i den lokala behållaren, som i Azure, vilket innebär att den skyddas av en åtkomst nyckel som definieras i *Function. JSON* med egenskapen `"authLevel": "function"`. Behållaren har inte publicerats i en Function-app i Azure, men nyckeln är inte tillgänglig ännu. Om du vill testa lokalt, stoppa Docker, ändra egenskapen Authorization till `"authLevel": "anonymous"`, återskapa avbildningen och starta om Docker. Återställ sedan `"authLevel": "function"` i *Function. JSON*. Mer information finns i [Authorization Keys](functions-bindings-http-webhook-trigger.md#authorization-keys).

    ![Platshållarbild som visar att behållaren körs lokalt](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

1. När du har verifierat Function-appen i behållaren, stoppa Docker med **Ctrl**+**C**.

## <a name="push-the-image-to-docker-hub"></a>Push-överför avbildningen till Docker Hub

Docker Hub är ett behållar register som är värd för avbildningar och tillhandahåller avbildnings-och container tjänster. Om du vill dela din avbildning, som inkluderar distribution till Azure, måste du skicka den till ett register.

1. Om du inte redan har loggat in på Docker gör du det med kommandot [Docker login](https://docs.docker.com/engine/reference/commandline/login/) och ersätter `<docker_id>` med Docker-ID. Med det här kommandot uppmanas du att ange ditt användar namn och lösen ord. Meddelandet "inloggningen lyckades" bekräftar att du är inloggad.

    ```
    docker login
    ```
    
1. När du har loggat in skickar du avbildningen till Docker Hub med hjälp av kommandot [Docker push](https://docs.docker.com/engine/reference/commandline/push/) och ersätter `<docker_id>` med ditt Docker-ID.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. Beroende på din nätverks hastighet kan det ta några minuter att skicka avbildningen första gången (det går mycket snabbare att överföra efterföljande ändringar). Medan du väntar kan du fortsätta till nästa avsnitt och skapa Azure-resurser i en annan terminal.

## <a name="create-supporting-azure-resources-for-your-function"></a>Skapa stöd för Azure-resurser för din funktion

Om du vill distribuera funktions koden till Azure måste du skapa tre resurser:

- En resurs grupp, som är en logisk behållare för relaterade resurser.
- Ett Azure Storage konto som upprätthåller tillstånd och annan information om dina projekt.
- En Azure Functions-app som tillhandahåller miljön för att köra funktions koden. En Function-app mappar till ditt lokala Function-projekt och gör att du kan gruppera funktioner som en logisk enhet för enklare hantering, distribution och delning av resurser.

Du kan använda Azure CLI-kommandon för att skapa dessa objekt. Varje kommando innehåller JSON-utdata när den har slutförts.

1. Logga in på Azure med kommandot [AZ login](/cli/azure/reference-index#az-login) :

    ```azurecli
    az login
    ```
    
1. Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resurs grupp med namnet `AzureFunctionsContainers-rg` i `westeurope` region. (Du skapar vanligt vis resurs gruppen och resurserna i en region nära dig, med hjälp av en tillgänglig region från kommandot `az account list-locations`.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Du kan inte vara värd för Linux-och Windows-appar i samma resurs grupp. Om du har en befintlig resurs grupp med namnet `AzureFunctionsContainers-rg` med en Windows Function-app eller en webbapp måste du använda en annan resurs grupp.
    
1. Skapa ett allmänt lagrings konto i din resurs grupp och region med hjälp av kommandot [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) . I följande exempel ersätter du `<storage_name>` med ett globalt unikt namn som passar dig. Namn måste innehålla mellan 3 och 24 tecken och endast små bokstäver. `Standard_LRS` anger ett typiskt allmänt syftes konto.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Lagrings kontot förväntar sig bara några USD cent för den här självstudien.
    
1. Använd kommandot för att skapa en Premium-plan för Azure Functions med namnet `myPremiumPlan` på pris nivån på **elastisk Premium 1** (`--sku EP1`) i regionen Europa, västra (`-location westeurope`), eller Använd en lämplig region nära dig) och i en Linux-behållare (`--is-linux`).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    Linux-värd för anpassade funktions behållare stöds på [dedikerade (App Service) planer](functions-scale.md#app-service-plan) och [Premium-planer](functions-premium-plan.md#features). Vi använder Premium planen här, som kan skalas efter behov. Mer information om värdfunktioner finns i [Azure Functions hosting plans comparison](functions-scale.md) (Jämförelse av Azure Functions-värdplaner). Information om hur du beräknar kostnader finns i [prissättnings sidan för funktioner](https://azure.microsoft.com/pricing/details/functions/).

    Kommandot etablerar även en associerad Azure Application insikts-instans i samma resurs grupp, som du kan använda för att övervaka din Function-app och Visa loggar. Mer information finns i [övervaka Azure Functions](functions-monitoring.md). Instansen debiteras inga kostnader förrän du aktiverar den.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Skapa och konfigurera en Function-app på Azure med avbildningen

En Function-app på Azure hanterar körningen av dina funktioner i värd planen. I det här avsnittet använder du Azure-resurserna från föregående avsnitt för att skapa en Function-app från en avbildning på Docker Hub och konfigurera den med en anslutnings sträng till Azure Storage.

1. Skapa Functions-appen med kommandot [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) . I följande exempel ersätter du `<storage_name>` med det namn som du använde i föregående avsnitt för lagrings kontot. Ersätt också `<app_name>` med ett globalt unikt namn som passar dig, och `<docker_id>` med ditt Docker-ID.

    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
    Parametern *Deployment-container-Image-Name* anger vilken avbildning som ska användas för Function-appen. Du kan använda kommandot [AZ functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) för att visa information om den avbildning som används för distribution. Du kan också använda kommandot [AZ functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) för att distribuera från en annan avbildning.

1. Hämta anslutnings strängen för det lagrings konto som du skapade med kommandot [AZ Storage Account show-Connection-String](/cli/azure/storage/account) och tilldela det till en Shell-variabel `storageConnectionString`:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Lägg till den här inställningen i Function-appen med hjälp av kommandot [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) . I följande kommando ersätter du `<app_name>` med namnet på din Function-app och ersätter `<connection_string>` med anslutnings strängen från föregående steg (en lång kodad sträng som börjar med "DefaultEndpointProtocol ="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

1. Funktionen kan nu använda den här anslutnings strängen för att komma åt lagrings kontot.

> [!TIP]
> I bash kan du använda en gränssnitts variabel för att avbilda anslutnings strängen i stället för att använda Urklipp. Använd först följande kommando för att skapa en variabel med anslutnings strängen:
> 
> ```bash
> storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
> ```
> 
> Referera sedan till variabeln i det andra kommandot:
> 
> ```azurecli
> az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
> ```

> [!NOTE]    
> Om du publicerar en anpassad avbildning till ett privat behållar konto bör du i stället använda miljövariabler i Dockerfile för anslutnings strängen. Mer information finns i avsnittet om [miljö](https://docs.docker.com/engine/reference/builder/#env). Du bör också ange variablerna `DOCKER_REGISTRY_SERVER_USERNAME` och `DOCKER_REGISTRY_SERVER_PASSWORD`. Om du vill använda värdena måste du återskapa avbildningen, push-överför avbildningen till registret och sedan starta om Function-appen på Azure.

## <a name="verify-your-functions-on-azure"></a>Verifiera dina funktioner i Azure

Med den avbildning som distribueras till Function-appen på Azure kan du nu anropa funktionen via HTTP-begäranden. Eftersom definitionen av *Function. JSON* innehåller egenskapen `"authLevel": "function"`måste du först hämta åtkomst nyckeln (kallas även "funktions nyckel") och ta med den som en URL-parameter i alla begär anden till slut punkten.

1. Hämta funktions webb adressen med åtkomst nyckeln (Function) med hjälp av Azure Portal eller med hjälp av Azure CLI med kommandot `az rest`.)

    # <a name="portal"></a>[Portal](#tab/portal)

    1. Logga in på Azure Portal och leta sedan reda på din Function-app genom att ange namnet på din app i rutan **Sök** högst upp på sidan. I resultaten väljer du **App Service** resursen.

    1. Välj namnet på din funktion under **funktioner (skrivskyddad)** i den vänstra navigerings panelen.

    1. I informations panelen väljer du **</> Hämta funktions webb adress**:
    
        ![URL-kommandot Hämta funktion på Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)   

    1. I popup-fönstret väljer du **Standard (funktions nyckel)** och sedan **Kopiera**. Nyckeln är en sträng med tecken som följer `?code=`.

        ![Kopiera funktions webb adressen från Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key-popup.png)   

    > [!NOTE]  
    > Eftersom din Function-app distribueras som en behållare kan du inte göra ändringar i funktions koden i portalen. Du måste i stället uppdatera projektet i den lokala avbildningen, push-överför avbildningen till registret igen och sedan distribuera om till Azure. Du kan ställa in kontinuerlig distribution i ett senare avsnitt.
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

    1. Skapa en URL-sträng i följande format, ersätta `<subscription_id>`, `<resource_group>`och `<app_name>` med ditt Azure-prenumerations-ID, resurs gruppen för din Function-app och namnet på din Function-app:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        URL-adressen kan till exempel se ut så här:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > För enkelhetens skull kan du i stället tilldela URL: en till en miljö variabel och använda den i kommandot `az rest`.
    
    1. Kör följande `az rest` kommando (tillgängligt i Azure CLI-versionen 2.0.77 och senare) och ersätt `<uri>` med URI-strängen från det sista steget, inklusive citat tecken:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. Kommandots utdata är funktions tangenten. URL: en för fullständig funktion `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`, ersätter `<app_name>`, `<function_name>`och `<key>` med dina egna värden.
    
        > [!NOTE]
        > Nyckeln som hämtas här är *värd* nyckeln som fungerar för alla funktioner i functions-appen. metoden som visas för portalen hämtar bara nyckeln för den enda funktionen.

    ---

1. Klistra in funktions webb adressen i webbläsarens Adress fält och Lägg till parametern `&name=Azure` i slutet av denna URL. Text som "Hej Azure" ska visas i webbläsaren.

    ![Funktionssvar i webbläsaren.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Om du vill testa auktorisering tar du bort koden = parameter från URL: en och kontrollerar att du inte får något svar från funktionen.


## <a name="enable-continuous-deployment-to-azure"></a>Aktivera kontinuerlig distribution till Azure

Du kan aktivera Azure Functions att automatiskt uppdatera distributionen av en avbildning varje gång du uppdaterar avbildningen i registret.

1. Aktivera kontinuerlig distribution med hjälp av kommandot [AZ functionapp Deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) , och ersätt `<app_name>` med namnet på din Function-app:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Det här kommandot aktiverar kontinuerlig distribution och returnerar distributions-webhook-URL: en. (Du kan hämta denna URL vid ett senare tillfälle genom att använda kommandot [AZ functionapp Deployment container show-CD-URL](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) .)

1. Kopiera distributions-webhook-URL: en till Urklipp.

1. Öppna [Docker Hub](https://hub.docker.com/), logga in och välj **databaser** i navigerings fältet. Leta upp och välj bild, Välj fliken **Webhooks** , ange ett **webhook-namn**, klistra in URL: en i **webhook-URL**och välj sedan **skapa**:

    ![Lägg till webhooken i din DockerHub-lagrings platsen](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Med webhook-uppsättningen, Azure Functions distribuerar avbildningen igen när du uppdaterar den i Docker Hub.

## <a name="enable-ssh-connections"></a>Aktivera SSH-anslutningar

SSH möjliggör säker kommunikation mellan en container och en klient. Med SSH aktiverat kan du ansluta till din behållare med hjälp av App Service Advanced tools (kudu). För att göra det enkelt att ansluta till din behållare med hjälp av SSH tillhandahåller Azure Functions en bas avbildning med SSH redan aktive rad. Du behöver bara redigera din Dockerfile, sedan återskapa och omdistribuera avbildningen. Sedan kan du ansluta till behållaren via avancerade verktyg (kudu)

1. I din Dockerfile lägger du till sträng `-appservice` till bas avbildningen i `FROM`s instruktionen:

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

    Skillnaderna mellan bas avbildningarna beskrivs i [själv studie kursen app Services anpassade Docker-avbildningar](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

1. Återskapa avbildningen med hjälp av `docker build` kommandot igen och ersätt `<docker_id>` med ditt Docker-ID:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Push-överför den uppdaterade avbildningen till Docker Hub, vilket bör ta avsevärt kortare tid än den första push-gränsen som bara de uppdaterade segmenten i bilden behöver överföras.

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions distribuerar automatiskt om avbildningen till Functions-appen. processen äger rum på mindre än en minut.

1. Öppna `https://<app_name>.scm.azurewebsites.net/`i en webbläsare och ersätt `<app_name>` med ditt unika namn. Den här URL: en är kudu-slutpunkten (Advanced tools) för din Function app-behållare.

1. Logga in på ditt Azure-konto och välj sedan **SSH** för att upprätta en anslutning till behållaren. Det kan ta en stund att ansluta. om Azure fortfarande håller på att uppdatera behållar avbildningen.

1. När en anslutning har upprättats med din behållare kör du kommandot `top` för att visa de processer som körs. 

    ![Linux Top-kommando som körs i en SSH-session](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Skriv till en Azure Storage kö

Med Azure Functions kan du ansluta dina funktioner till andra Azure-tjänster och-resurser som behöver skriva din egen integrerings kod. Dessa *bindningar*, som representerar både indata och utdata, deklareras i funktions definitionen. Data från bindningar tillhandahålls till funktionen som parametrar. En *utlösare* är en särskild typ av ingående bindning. Även om en funktion bara har en utlösare, kan den ha flera indata och utdata-bindningar. Mer information finns i [Azure Functions utlösare och bindningar begrepp](functions-triggers-bindings.md).

I det här avsnittet visas hur du integrerar din funktion med en Azure Storage kö. Den utgående bindning som du lägger till i den här funktionen skriver data från en HTTP-begäran till ett meddelande i kön.

## <a name="retrieve-the-azure-storage-connection-string"></a>Hämta Azure Storage anslutnings sträng

Tidigare skapade du ett Azure Storage-konto som ska användas av Function-appen. Anslutnings strängen för det här kontot lagras på ett säkert sätt i appinställningar i Azure. Genom att Hämta inställningen till den *lokala. Settings. JSON* -filen kan du använda den för att skriva till en lagrings kö i samma konto när funktionen körs lokalt. 

1. Kör följande kommando från roten av projektet och ersätt `<app_name>` med namnet på din Function-app från föregående snabb start. Det här kommandot skriver över alla befintliga värden i filen.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Öppna *Local. Settings. JSON* och leta upp värdet med namnet `AzureWebJobsStorage`, vilket är anslutnings strängen för lagrings kontot. Du använder namnet `AzureWebJobsStorage` och anslutnings strängen i andra avsnitt i den här artikeln.

> [!IMPORTANT]
> Eftersom *Local. Settings. JSON* innehåller hemligheter som hämtats från Azure, ska du alltid utesluta den här filen från käll kontroll. Filen *. gitignore* som skapas med ett lokalt Functions-projekt utesluter filen som standard.

### <a name="add-an-output-binding-to-functionjson"></a>Lägg till en utgående bindning till function. JSON

I Azure Functions kräver varje typ av bindning en `direction`, `type`och en unik `name` som ska definieras i *Function. JSON* -filen. Din *Function. JSON* innehåller redan en inkommande bindning för typen "httpTrigger" och en utgående bindning för http-svaret. Om du vill lägga till en bindning till en lagrings kö ändrar du filen enligt följande, som lägger till en utgående bindning för typen "kö", där kön visas i koden som ett indataargument med namnet `msg`. Queue-bindningen kräver också namnet på kön som ska användas, i det här fallet `outqueue`och namnet på de inställningar som innehåller anslutnings strängen, i det här fallet `AzureWebJobStorage`.

::: zone pivot="programming-language-csharp"

I ett C# klass biblioteks projekt definieras bindningarna som binding-attribut i funktions metoden. *Function. JSON* -filen genereras sedan automatiskt baserat på dessa attribut.

1. För köns bindning kör du följande [dotNet Add Package](/dotnet/core/tools/dotnet-add-package) -kommando för att lägga till lagrings tilläggs paketet i projektet.

    ```
    dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
    ```

1. Öppna filen *HttpTrigger.cs* och Lägg till följande `using`-instruktion:

    ```cs
    using Microsoft.Azure.WebJobs.Extensions.Storage;
    ```
    
1. Lägg till följande parameter i definitions metoden för `Run`:
    
    ```csharp
    [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
    ```
    
    Den `Run` metod definitionen bör nu matcha följande kod:
    
    ```csharp
    [FunctionName("HttpTrigger")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
        [Queue("outqueue"), StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
    ```

Parametern `msg` är en `ICollector<T>` typ, som representerar en samling meddelanden som skrivs till en utgående bindning när funktionen slutförs. I det här fallet är utdata en lagrings kö med namnet `outqueue`. Anslutnings strängen för lagrings kontot anges av `StorageAccountAttribute`. Det här attributet anger den inställning som innehåller lagrings kontots anslutnings sträng och kan tillämpas på klass-, metod-eller parameter nivå. I det här fallet kan du utelämna `StorageAccountAttribute` eftersom du redan använder standard lagrings kontot.

::: zone-end

::: zone pivot="programming-language-javascript"

Uppdatera *Function. JSON* så att den matchar följande genom att lägga till en Queue-bindning efter http-bindningen:

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

Uppdatera *Function. JSON* så att den matchar följande genom att lägga till en Queue-bindning efter http-bindningen:

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

Uppdatera *Function. JSON* så att den matchar följande genom att lägga till en Queue-bindning efter http-bindningen:

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

Uppdatera *Function. JSON* så att den matchar följande genom att lägga till en Queue-bindning efter http-bindningen:

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

## <a name="add-code-to-use-the-output-binding"></a>Lägg till kod för att använda utgående bindning

När bindningen har definierats visas namnet på bindningen, i det här fallet `msg`, i funktions koden som ett argument (eller i `context`-objektet i Java Script och TypeScript). Du kan sedan använda variabeln för att skriva meddelanden till kön. Du måste skriva kod för autentisering, få en referens till kön eller skriva data. Alla dessa integrations aktiviteter hanteras bekvämt i Azure Functions Runtime-och Queue-bindning.

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

1. I rotmappen kör `docker build` igen och den här gången uppdaterar du versionen i taggen för att `v1.0.1`. Som tidigare ersätter du `<docker_id>` med ditt konto-ID för Docker Hub:

    ```
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1
    ```
    
1. Skicka tillbaka den uppdaterade avbildningen till lagrings platsen med `docker push`:

    ```
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Eftersom du konfigurerade kontinuerlig leverans uppdaterar avbildningen i registret igen automatiskt din Function-app i Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Visa meddelandet i Azure Storage kön

I en webbläsare använder du samma URL som innan du anropar din funktion. Webbläsaren ska visa samma svar som tidigare eftersom du inte har ändrat den delen av funktions koden. Den tillagda koden skrev dock ett meddelande med hjälp av parametern `name` URL till `outqueue` lagrings kön.

Du kan visa kön i [Azure Portal](../storage/queues/storage-quickstart-queues-portal.md) eller i [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Du kan också visa kön i Azure CLI enligt beskrivningen i följande steg:

1. Öppna funktions projektets *lokala. Ange. JSON* -fil och kopiera värdet för anslutnings strängen. Kör följande kommando i ett terminalfönster-eller kommando fönster för att skapa en miljö variabel med namnet `AZURE_STORAGE_CONNECTION_STRING`, och klistra in din speciella anslutnings sträng i stället för `<connection_string>`. (Denna miljö variabel innebär att du inte behöver ange anslutnings strängen för varje efterföljande kommando med hjälp av argumentet `--connection-string`.)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmd"></a>[Kommandot](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. Valfritt Använd kommandot [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) för att Visa lagrings köerna i ditt konto. Utdata från det här kommandot ska innehålla en kö med namnet `outqueue`, som skapades när funktionen skrev sitt första meddelande till kön.
    
    # <a name="bash"></a>[bash](#tab/bash)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Kommandot](#tab/cmd)
    
    ```azurecli
    az storage queue list --output tsv
    ```
    
    ---

1. Använd kommandot [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) för att visa meddelanden i den här kön, vilket bör vara det första namnet du använde när du testar funktionen tidigare. Kommandot hämtar det första meddelandet i kön i [base64-kodning](functions-bindings-storage-queue-trigger.md#encoding), så du måste också avkoda meddelandet för att visa som text.

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Kommandot](#tab/cmd)
    
    Eftersom du måste referera till meddelande samlingen och avkoda från base64 kör du PowerShell och använder PowerShell-kommandot.

    ---

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta att arbeta med Azure-funktionen med hjälp av de resurser som du skapade i den här självstudien kan du lämna alla dessa resurser på plats. Eftersom du har skapat en Premium-plan för Azure Functions får du en eller två USD per dag i löpande kostnad.

Ta bort den `AzureFunctionsContainer-rg` resurs gruppen om du vill rensa alla resurser i gruppen för att undvika kontinuerliga kostnader: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Nästa steg

+ [Övervaknings funktioner](functions-monitoring.md)
+ [Skalnings-och värd alternativ](functions-scale.md)
+ [Kubernetes-baserad server lös värd](functions-kubernetes-keda.md)
