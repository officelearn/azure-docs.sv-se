---
title: Skapa en funktion i Azure som svarar på HTTP-begäranden
description: Lär dig hur du skapar en funktion från kommandoraden och publicerar sedan det lokala projektet till serverlös värd i Azure Functions.
ms.date: 03/30/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 1e3ce26894e9e89d196c068bd32245c8c891b2e2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255557"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Snabbstart: Skapa en funktion i Azure som svarar på HTTP-begäranden

I den här artikeln använder du kommandoradsverktyg för att skapa en funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverlösa miljön i Azure Functions. Om du slutför den här snabbstarten medför en liten kostnad på några USD-cent eller mindre i ditt Azure-konto.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Det finns också en [Visual Studio-kodbaserad version](functions-create-first-function-vs-code.md) av den här artikeln.
::: zone-end  

::: zone pivot="programming-language-java"  
> [!NOTE]
> Om Maven inte är din föredragna utvecklingsverktyg, kolla in våra liknande tutorials för Java devlopers med [Gradle](/azure/azure-functions/functions-create-first-java-gradle), [IntelliJ IDEA](/azure/java/intellij/azure-toolkit-for-intellij-quickstart-functions) och [VS Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java).
::: zone-end  

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-a-local-function-project"></a>Skapa ett lokalt funktionsprojekt

I Azure Functions är ett funktionsprojekt en behållare för en eller flera enskilda funktioner som var och en svarar på en viss utlösare. Alla funktioner i ett projekt har samma lokala konfigurationer och värdkonfigurationer. I det här avsnittet skapar du ett funktionsprojekt som innehåller en enda funktion.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Kör `func init` kommandot, enligt följande, för att skapa ett funktionsprojekt i en mapp med namnet *LocalFunctionProj* med den angivna körningen:  
::: zone-end  
::: zone pivot="programming-language-python"  
```
func init LocalFunctionProj --python
```
::: zone-end  
::: zone pivot="programming-language-csharp"  
```
func init LocalFunctionProj --dotnet
```
::: zone-end  
::: zone pivot="programming-language-javascript"  
```
func init LocalFunctionProj --javascript
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```
func init LocalFunctionProj --typescript
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```
func init LocalFunctionProj --powershell
```
::: zone-end    
::: zone pivot="programming-language-java"  
Kör följande kommando i en tom mapp för att skapa ett funktionsprojekt utifrån en [Maven-arketyp](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype 
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" 
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" 
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
Navigera till projektmappen:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```
cd LocalFunctionProj
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
cd fabrikam-functions
```
::: zone-end  
Den här mappen innehåller olika filer för projektet, inklusive konfigurationsfiler med namnet [local.settings.json](functions-run-local.md#local-settings-file) och [host.json](functions-host-json.md). Eftersom *local.settings.json* kan innehålla hemligheter som hämtats från Azure, är filen undantagen från källkontrollen som standard i *.gitignore-filen.*

[!INCLUDE [functions-cli-add-function](../../includes/functions-cli-add-function.md)]

### <a name="optional-examine-the-file-contents"></a>(Valfritt) Granska filinnehållet

Om du vill kan du hoppa till [Kör funktionen lokalt](#run-the-function-locally) och undersöka filinnehållet senare.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* innehåller `Run` en metod som tar emot `req` begärandedata i variabeln är en [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) som är dekorerad med **HttpTriggerAttribute**, som definierar utlösarbeteendet. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Returobjektet är ett [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) som returnerar ett svarsmeddelande som antingen ett [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) eller ett [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Mer information finns i [Azure Functions HTTP-utlösare och bindningar](/azure/azure-functions/functions-bindings-http-webhook?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-java"
#### <a name="functionjava"></a>Funktion.java
*Function.java* innehåller `run` en metod som tar `request` emot begärandedata i variabeln är en [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) som är dekorerad med [HttpTrigger-anteckningen,](/java/api/com.microsoft.azure.functions.annotation.httptrigger) som definierar utlösarbeteendet. 

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java":::

Svarsmeddelandet genereras av [HTTPResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API.

#### <a name="pomxml"></a>pom.xml (på-i-till-samma

Inställningar för Azure-resurser som skapats för att vara värd för din `com.microsoft.azure` app definieras i **konfigurationselementet** i plugin-programmet med en **groupId** i den genererade pom.xml-filen. Konfigurationselementet nedan instruerar till exempel en Maven-baserad distribution `java-functions-group` att skapa `westus` en funktionsapp i resursgruppen i regionen. Själva funktionsappen körs i `java-functions-app-service-plan` Windows som finns i planen, som som standard är en serverlös förbrukningsplan.    

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="116-155":::

Du kan ändra dessa inställningar för att styra hur `runtime.os` resurser `windows` `linux` skapas i Azure, till exempel genom att ändra från till före den första distributionen. En fullständig lista över inställningar som stöds av Maven-plugin-programmet finns i [konfigurationsinformationen](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).

#### <a name="functiontestjava"></a>FunctionTest.java

Arketypen genererar också ett enhetstest för din funktion. När du ändrar funktionen för att lägga till bindningar eller lägga till nya funktioner i projektet måste du också ändra testerna i filen *FunctionTest.java.*
::: zone-end  
::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* innehåller `main()` en Python-funktion som utlöses enligt konfigurationen i *function.json*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

För en HTTP-utlösare tar funktionen `req` emot begärandedata i variabeln enligt definitionen i *function.json*. `req`är en instans av [klassen azure.functions.HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Returobjektet, definierat som `$return` i *function.json*, är en instans av klassen [azure.functions.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Mer information finns i [Azure Functions HTTP-utlösare och bindningar](/azure/azure-functions/functions-bindings-http-webhook?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index.js

*index.js exporterar* en funktion som utlöses enligt konfigurationen i *function.json*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

För en HTTP-utlösare tar funktionen `req` emot begärandedata i variabeln enligt definitionen i *function.json*. Returobjektet, definierat som `$return` i *function.json*, är svaret. Mer information finns i [Azure Functions HTTP-utlösare och bindningar](/azure/azure-functions/functions-bindings-http-webhook?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index.ts

*index.ts exporterar* en funktion som utlöses enligt konfigurationen i *function.json*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

För en HTTP-utlösare tar funktionen `req` emot begärandedata i variabeln av typen **HttpRequest** enligt definitionen i *function.json*. Returobjektet, definierat som `$return` i *function.json*, är svaret. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>run.ps1

*run.ps1* definierar ett funktionsskript som utlöses enligt konfigurationen i *function.json*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

För en HTTP-utlösare tar funktionen `$Request` emot begärandedata som skickas till param som definierats i *function.json*. Returobjektet, definierat som `Response` i *function.json*, skickas till `Push-OutputBinding` cmdleten som svar. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*function.json* är en konfigurationsfil som `bindings` definierar indata och utdata för funktionen, inklusive utlösartypen. 
::: zone-end

::: zone pivot="programming-language-python"
Du kan `scriptFile` ändra till att anropa en annan Python-fil om så önskas.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Varje bindning kräver en riktning, en typ och ett unikt namn. HTTP-utlösaren har en [`httpTrigger`](functions-bindings-http-webhook-trigger.md) indatabindning [`http`](functions-bindings-http-webhook-output.md)av typ och utdatabindning av typen .
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"    
## <a name="create-supporting-azure-resources-for-your-function"></a>Skapa stöd för Azure-resurser för din funktion

Innan du kan distribuera funktionskoden till Azure måste du skapa tre resurser:

- En resursgrupp, som är en logisk behållare för relaterade resurser.
- Ett lagringskonto som upprätthåller tillstånd och annan information om dina projekt.
- En funktionsapp som tillhandahåller miljön för att köra din funktionskod. En funktionsapp mappas till ditt lokala funktionsprojekt och låter dig gruppera funktioner som en logisk enhet för enklare hantering, distribution och delning av resurser.

Använd följande Azure CLI-kommandon för att skapa dessa objekt. Varje kommando ger JSON-utdata när det är klart.

Om du inte redan har gjort det loggar du in på Azure med kommandot [az login:](/cli/azure/reference-index#az-login)

    ```azurecli
    az login
    ```
    
Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). I följande exempel skapas `AzureFunctionsQuickstart-rg` en `westeurope` resursgrupp med namnet i regionen. (Du skapar vanligtvis resursgruppen och resurserna i en region nära `az account list-locations` dig med hjälp av en tillgänglig region från kommandot.)

```azurecli
az group create --name AzureFunctionsQuickstart-rg --location westeurope
```

> [!NOTE]
> Du kan inte vara värd för Linux- och Windows-appar i samma resursgrupp. Om du har en `AzureFunctionsQuickstart-rg` befintlig resursgrupp med namnet med en Windows-funktionsapp eller webbapp måste du använda en annan resursgrupp.
 
    
Skapa ett allmänt lagringskonto i resursgruppen och regionen med kommandot [skapa az-lagringskonto.](/cli/azure/storage/account#az-storage-account-create) I följande exempel `<STORAGE_NAME>` ersätter du med ett globalt unikt namn som passar dig. Namnen får innehålla endast tre till 24 teckens siffror och gemener. `Standard_LRS`anger ett konto för allmänt ändamål som [stöds av Functions](storage-considerations.md#storage-account-requirements).

```azurecli
az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
```

Lagringskontot ådrar sig bara några cent (USD) för denna snabbstart.
    
Skapa funktionsappen med kommandot [az functionapp create.](/cli/azure/functionapp#az-functionapp-create) I följande exempel `<STORAGE_NAME>` ersätter du med namnet på kontot som `<APP_NAME>` du använde i föregående steg och ersätter med ett globalt unikt namn som passar dig. `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. 
::: zone-end  

::: zone pivot="programming-language-python"  
Om du använder Python 3.8 `3.8` `--functions_version` ändrar du `--runtime-version` till och till `3`.

Om du använder Python 3.6 ändrar du `--runtime-version` till `3.6`.

```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Om du använder Node.js 8, ändra `--runtime-version` också till `8`.


```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-csharp"  
```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-powershell"  
```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
Det här kommandot skapar en funktionsapp som körs i din angivna språkkörning under [Azure Functions Consumption Plan](functions-scale.md#consumption-plan), som är gratis för den mängd användning du ådrar dig här. Kommandot etablerar också en associerad Azure Application Insights-instans i samma resursgrupp, med vilken du kan övervaka din funktionsapp och visa loggar. Mer information finns i [Övervaka Azure-funktioner](functions-monitoring.md). Instansen medför inga kostnader förrän du aktiverar den.
    
## <a name="deploy-the-function-project-to-azure"></a>Distribuera funktionsprojektet till Azure
::: zone-end  

::: zone pivot="programming-language-typescript"  
Innan du använder Core Tools för att distribuera projektet till Azure skapar du en produktionsklar version av JavaScript-filer från TypeScript-källfilerna.

Följande kommando förbereder Ditt TypeScript-projekt för distribution:

```
npm run build:production 
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
Med nödvändiga resurser på plats är du nu redo att distribuera ditt lokala funktionsprojekt till funktionsappen i Azure med hjälp av kommandot [func azure functionapp publish.](functions-run-local.md#project-file-deployment) Ersätt `<APP_NAME>` med namnet på appen i följande exempel.

```
func azure functionapp publish <APP_NAME>
```

Om du ser felet "Det går inte att hitta app med namn ...", vänta några sekunder och försök `az functionapp create` igen, eftersom Azure kanske inte har initierat appen helt efter det föregående kommandot.

Kommandot Publicera visar resultat som liknar följande utdata (trunkerade för enkelhetens skull):

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
</pre>

::: zone-end  
::: zone pivot="programming-language-java"  
## <a name="deploy-the-function-project-to-azure"></a>Distribuera funktionsprojektet till Azure

En funktionsapp och relaterade resurser skapas i Azure när du först distribuerar ditt funktionsprojekt. Inställningarna för de Azure-resurser som skapats för att vara värd för din app definieras i [filen pom.xml](#pomxml). I den här artikeln accepterar du standardinställningarna.

> [!TIP]
> Om du vill skapa en funktionsapp `runtime.os` som körs på Linux `windows` i `linux`stället för Windows ändrar du elementet i filen pom.xml från till . Köra Linux i en förbrukningsplan stöds i [dessa regioner](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions). Du kan inte ha appar som körs på Linux och appar som körs i Windows i samma resursgrupp.

Innan du kan distribuera använder du azure [CLI-kommandot az login](/cli/azure/authenticate-azure-cli) för att logga in på din Azure-prenumeration. 

```azurecli
az login
```

Använd följande kommando för att distribuera projektet till en ny funktionsapp. 

```
mvn azure-functions:deploy
```

Detta skapar följande resurser i Azure:

+ Resursgrupp. Namngiven som _java-funktioner-grupp_.
+ Lagringskonto. Krävs av Functions. Namnet genereras slumpmässigt baserat på krav på lagringskontonamn.
+ Värdplan. Serverlös hosting för din funktionsapp i _westus-regionen._ Namnet är _java-funktioner-app-service-plan_.
+ Funktionsapp. En funktionsapp är distributions- och körningsenheten för dina funktioner. Namnet genereras slumpmässigt baserat på att du är din _artifactId,_ läggs till med ett slumpmässigt genererat nummer. 

Distributionen paketerar projektfilerna och distribuerar dem till den nya funktionsappen med [zip-distribution](functions-deployment-technologies.md#zip-deploy). Koden körs från distributionspaketet i Azure.
::: zone-end

## <a name="invoke-the-function-on-azure"></a>Anropa funktionen på Azure

Eftersom funktionen använder en HTTP-utlösare anropar du den genom att göra en HTTP-begäran till webbadressen i webbläsaren eller med ett verktyg som curl. I båda fallen `code` är URL-parametern din unika [funktionsnyckel](functions-bindings-http-webhook-trigger.md#authorization-keys) som tillåter anrop av funktionsslutpunkten.

# <a name="browser"></a>[Webbläsare](#tab/browser)

Kopiera den fullständiga **anropa-URL:en** som visas i utdata från publiceringskommandot till ett webbläsaradressfält, vilket lägger till frågeparametern `&name=Functions`. Webbläsaren ska visa liknande utdata som när du körde funktionen lokalt.

![Utdata för funktionen körs på Azure i en webbläsare](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[Curl](#tab/curl)

Kör [`curl`](https://curl.haxx.se/) med **url:en anropa**och `&name=Functions`lägga till parametern . Utdata för kommandot ska vara texten "Hej funktioner".

![Utdata för funktionen körs på Azure med curl](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Om du vill visa loggar i nära realtid för en publicerad funktionsapp använder du [Live Metrics Stream för Application Insights](functions-monitoring.md#streaming-logs).

## <a name="clean-up-resources"></a>Rensa resurser

Om du fortsätter till nästa steg, [Lägg till en Azure Storage-köutdatabindning,](functions-add-output-binding-storage-queue-cli.md)behåll alla dina resurser på plats när du bygger vidare på vad du redan har gjort.

Annars använder du följande kommando för att ta bort resursgruppen och alla dess inneslutna resurser för att undvika att ådra sig ytterligare kostnader.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage-kö](functions-add-output-binding-storage-queue-cli.md)
