---
title: Anslut din Java-funktion till Azure Storage
description: Lär dig hur du ansluter en HTTP-utlöst Java-funktion till Azure Storage med hjälp av en kö Storage utgående bindning.
author: ggailey777
ms.author: glenga
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: azure-functions
manager: gwallace
ms.openlocfilehash: c78630af7d09cc911862c8e823c5dfeee9cabbd9
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333467"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Anslut din Java-funktion till Azure Storage

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Den här artikeln visar hur du integrerar funktionen som du skapade i [föregående snabb starts artikel](functions-create-first-java-maven.md) med en Azure Storage kö. Den utgående bindning som du lägger till i den här funktionen skriver data från en HTTP-begäran till ett meddelande i kön.

De flesta bindningar kräver en lagrad anslutnings sträng som används för att få åtkomst till den kopplade tjänsten. För att göra anslutningen enklare använder du det lagrings konto som du skapade med din Function-app. Anslutningen till det här kontot finns redan i en app-inställning med namnet `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Krav

Innan du börjar den här artikeln slutför du stegen i [del 1 av Java-snabb](functions-create-first-java-maven.md)starten.

## <a name="download-the-function-app-settings"></a>Ladda ned appens funktions inställningar

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Aktivera tilläggs paket

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Nu kan du lägga till bindningen för Storage-utdata i projektet.

## <a name="add-an-output-binding"></a>Lägg till en utdatabindning

I ett Java-projekt definieras bindningarna som bindnings anteckningar i funktions metoden. Filen *Function. JSON* genereras sedan automatiskt baserat på dessa anteckningar.

Bläddra till platsen för funktions koden under _src/main/Java_, öppna filen *Function. java* -projekt och Lägg till följande parameter i metod definitionen för `run`:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

Parametern `msg` är en [`OutputBinding<T>`-](/java/api/com.microsoft.azure.functions.outputbinding) typ som representerar en samling strängar som skrivs som meddelanden till en utgående bindning när funktionen slutförs. I det här fallet är utdata en lagrings kö med namnet `outqueue`. Anslutnings strängen för lagrings kontot anges av metoden `connection`. I stället för själva anslutnings strängen skickar du den program inställning som innehåller anslutnings strängen för lagrings kontot.

Metod definitionen för `run` bör nu se ut som i följande exempel:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```

## <a name="add-code-that-uses-the-output-binding"></a>Lägg till kod som använder utdatabindning

Nu kan du använda den nya parametern `msg` för att skriva till utgående bindning från funktions koden. Lägg till följande kodrad innan du lyckas med att lägga till värdet för `name` till bindningen för @no__t 1-utdata.

```java
msg.setValue(name);
```

När du använder en utgående bindning behöver du inte använda Azure Storage SDK-koden för autentisering, hämta en referens till kön eller skriva data. Bindningarna Functions Runtime och Queue output utför dessa uppgifter åt dig.

Metoden `run` bör nu se ut som i följande exempel:

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    context.getLogger().info("Java HTTP trigger processed a request.");

    // Parse query parameter
    String query = request.getQueryParameters().get("name");
    String name = request.getBody().orElse(query);

    if (name == null) {
        return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
    } else {
        // Write the name to the message queue. 
        msg.setValue(name);

        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
}
```

## <a name="update-the-tests"></a>Uppdatera testerna

Eftersom archetype också skapar en uppsättning tester måste du uppdatera de här testerna för att hantera den nya `msg`-parametern i signaturen för `run`-metoden.  

Bläddra till platsen för test koden under _src/test/java_, öppna filen *Function. java* -projekt och ersätt kodraden under `//Invoke` med följande kod.

```java
@SuppressWarnings("unchecked")
final OutputBinding<String> msg = (OutputBinding<String>)mock(OutputBinding.class);

// Invoke
final HttpResponseMessage ret = new Function().run(req, msg, context);
``` 

Nu är du redo att testa den nya utgående bindningen lokalt.

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Som tidigare använder du följande kommando för att skapa projektet och starta Functions-körningen lokalt:

```bash
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]  
> Eftersom du har aktiverat tilläggs paket i Host. JSON laddades [lagrings bindnings tillägget](functions-bindings-storage-blob.md#packages---functions-2x) ned och installerades åt dig under starten, tillsammans med de andra Microsoft binding-tilläggen.

Som tidigare utlöser funktionen från kommando raden med hjälp av sväng i ett nytt terminalfönster:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Den här gången skapar bindningen för utdata också en kö med namnet `outqueue` i ditt lagrings konto och lägger till ett meddelande med samma sträng.

Sedan använder du Azure CLI för att visa den nya kön och kontrol lera att ett meddelande har lagts till. Du kan också visa din kö med hjälp av [Microsoft Azure Storage Explorer][Azure Storage Explorer] eller i [Azure Portal](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Distribuera om projektet 

Uppdatera den publicerade appen genom att köra följande kommando igen:  

```azurecli
mvn azure-functions:deploy
```

Igen kan du använda sväng för att testa den distribuerade funktionen. Som tidigare skickar du värdet `AzureFunctions` i bröd texten i POST-begäran till URL: en, som i det här exemplet:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Du kan [Granska lagrings köns meddelande](#query-the-storage-queue) igen för att kontrol lera att utmatnings bindningen genererar ett nytt meddelande i kön som förväntat.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

Du har uppdaterat din HTTP-utlöst funktion för att skriva data till en lagrings kö. Mer information om hur du utvecklar Azure Functions med Java finns i [Azure Functions Java Developer Guide](functions-reference-java.md) och [Azure Functions utlösare och bindningar](functions-triggers-bindings.md). Exempel på kompletta funktions projekt i Java finns i [exemplen för Java Functions](/samples/browse/?products=azure-functions&languages=Java). 

Sedan bör du aktivera Application Insights övervakning för din Function-app:

> [!div class="nextstepaction"]
> [Aktivera Application Insights-integrering](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/