---
title: Skapa Azure-funktioner på Linux med hjälp av en anpassad avbildning
description: Lär dig hur du skapar en Azure Functions som körs på en anpassad Linux-avbildning.
ms.date: 03/30/2020
ms.topic: tutorial
ms.custom: mvc
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 44ca8f721967b90be283f867f8656344ec3f1906
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673419"
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

Du kan följa den här självstudien på alla datorer som kör Windows, macOS eller Linux. Om du slutför självstudien kostar det några us-dollar i ditt Azure-konto.

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ Ett [Docker-ID](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ Kör `docker login` för att logga in på Docker. Det här kommandot misslyckas om Docker inte körs, i vilket fall starta docker och försök igen kommandot.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Skapa och testa projektet för lokala funktioner

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
I en terminal eller kommandotolk kör du följande kommando för det valda `LocalFunctionsProject`språket för att skapa ett funktionsappprojekt i en mapp med namnet .  
::: zone-end  
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
::: zone pivot="programming-language-java"  
Kör följande kommando i en tom mapp för att skapa ett funktionsprojekt utifrån en [Maven-arketyp](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-Ddocker"
```
---

Maven ber dig om värden som behövs för att slutföra generera projektet på distributionen.   
Ange följande värden när du uppmanas att göra det:

| Uppmaning | Värde | Beskrivning |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Ett värde som unikt identifierar ditt projekt i alla projekt, enligt [paketnamngivningsreglerna](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) för Java. |
| **artifactId (artefaktId)** | `fabrikam-functions` | Ett värde som är namnet på burken, utan versionsnummer. |
| **Version** | `1.0-SNAPSHOT` | Välj standardvärdet. |
| **Paket** | `com.fabrikam.functions` | Ett värde som är Java-paketet för den genererade funktionskoden. Använd standardvärdet. |

Skriv `Y` eller tryck på Retur för att bekräfta.

Maven skapar projektfilerna i en ny mapp med namnet _artifactId_, som i det här exemplet är `fabrikam-functions`. 
::: zone-end
Alternativet `--docker` genererar en `Dockerfile` för projektet, som definierar en lämplig anpassad behållare för användning med Azure Functions och den valda körningen.

Navigera till projektmappen:
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Lägg till en funktion i projektet med `--name` hjälp av följande kommando, där `--template` argumentet är det unika namnet på din funktion och argumentet anger funktionens utlösare. `func new`skapa en undermapp som matchar funktionsnamnet som innehåller en kodfil som är lämplig för projektets valda språk och en konfigurationsfil med namnet *function.json*.

```
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end  
Om du vill testa funktionen lokalt startar du den lokala Azure Functions-körningsvärden i roten för projektmappen: 
::: zone pivot="programming-language-csharp"  
```
func start --build  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
mvn clean package  
mvn azure-functions:run
```
::: zone-end
När du `HttpExample` ser slutpunkten visas i [`http://localhost:7071/api/HttpExample?name=Functions`](http://localhost:7071/api/HttpExample?name=Functions)utdata navigerar du till . Webbläsaren ska visa ett "hello"-meddelande `Functions`som ekar tillbaka `name` , det värde som anges till frågeparametern.

Använd **Ctrl**-**C** för att stoppa värden.

## <a name="build-the-container-image-and-test-locally"></a>Skapa behållaravbildningen och testa lokalt

(Valfritt) Undersök *Dockerfile" i roten på projektmappen. Dockerfile beskriver den miljö som krävs för att köra funktionsappen på Linux.  Den fullständiga listan över basavbildningar som stöds för Azure Functions finns på bassidan för [Azure Functions.](https://hub.docker.com/_/microsoft-azure-functions-base)
    
I rotprojektmappen kör [du kommandot docker build](https://docs.docker.com/engine/reference/commandline/build/) `azurefunctionsimage`och anger `v1.0.0`ett namn, och tagg, . Ersätt `<DOCKER_ID>` med ditt konto-ID för Docker Hub. Det här kommandot skapar Docker-avbildningen för containern.

```
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

När kommandot är klart kan du köra den nya behållaren lokalt.
    
Om du vill testa versionen kör du avbildningen i en `<DOCKER_ID` lokal behållare med kommandot [docker run, ersätter](https://docs.docker.com/engine/reference/commandline/run/) igen med Docker-ID:et och lägger till portarargumentet: `-p 8080:80`

```
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
När bilden körs i en lokal behållare `http://localhost:8080`öppnar du en webbläsare för , som ska visa platshållarbilden som visas nedan. Avbildningen visas nu eftersom din funktion körs i den lokala behållaren, som den skulle i Azure, vilket innebär att `"authLevel": "function"` den skyddas av en åtkomstnyckel enligt definitionen i *function.json* med egenskapen. Behållaren har ännu inte publicerats till en funktionsapp i Azure, så nyckeln är ännu inte tillgänglig. Om du vill testa mot den lokala behållaren, stoppa `"authLevel": "anonymous"`docker, ändra auktoriseringsegenskapen till , återskapa avbildningen och starta om docker. Återställ `"authLevel": "function"` sedan i *function.json*. Mer information finns i [auktoriseringsnycklar](functions-bindings-http-webhook-trigger.md#authorization-keys).

![Platshållarbild som anger att behållaren körs lokalt](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
När bilden körs i en lokal [`http://localhost:8080/api/HttpExample?name=Functions`](http://localhost:8080/api/HttpExample?name=Functions)behållare bläddrar du till , som ska visa samma "hello"-meddelande som tidigare. Eftersom Maven-arketypen genererar en HTTP-utlöst funktion som använder anonym auktorisering kan du fortfarande anropa funktionen även om den körs i behållaren. 
::: zone-end  

När du har verifierat funktionsappen i behållaren stoppar du dockern med **Ctrl**+**C**.

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

1. Logga in på ditt Azure-konto och välj sedan **SSH** för att upprätta en anslutning till behållaren. Det kan ta en stund att ansluta om Azure fortfarande uppdaterar behållaravbildningen.

1. När en anslutning har upprättats med `top` behållaren kör du kommandot för att visa de processer som körs. 

    ![Linux-toppkommando som körs i en SSH-session](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-an-azure-storage-queue"></a>Skriv till en Azure Storage-kö

Med Azure Functions kan du ansluta dina funktioner till andra Azure-tjänster och resurser som måste skriva din egen integrationskod. Dessa *bindningar*, som representerar både indata och utdata, deklareras i funktionsdefinitionen. Data från bindningar anges för funktionen som parametrar. En *utlösare* är en speciell typ av indatabindning. Även om en funktion bara har en utlösare kan den ha flera in- och utdatabindningar. Mer information finns i [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)begrepp .

Det här avsnittet visar hur du integrerar din funktion med en Azure Storage-kö. Utdatabindningen som du lägger till i den här funktionen skriver data från en HTTP-begäran till ett meddelande i kön.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

## <a name="add-code-to-use-the-output-binding"></a>Lägga till kod för att använda utdatabindningen

Med köbindningen definierad kan du nu `msg` uppdatera funktionen för att ta emot utdataparametern och skriva meddelanden till kön.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
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

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

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
