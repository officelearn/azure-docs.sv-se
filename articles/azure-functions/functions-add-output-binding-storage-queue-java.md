---
title: Ansluta java-funktionen till Azure Storage
description: Lär dig hur du ansluter en HTTP-utlöst Java-funktion till Azure Storage med hjälp av en kölagringsutdatabindning.
author: KarlErickson
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: 8ae69bfa7ed00e310205332e05c071158c5fc9a3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78272808"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Ansluta java-funktionen till Azure Storage

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Den här artikeln visar hur du integrerar funktionen som du skapade i den [tidigare snabbstartsartikeln](functions-create-first-java-maven.md) med en Azure Storage-kö. Utdatabindningen som du lägger till i den här funktionen skriver data från en HTTP-begäran till ett meddelande i kön.

De flesta bindningar kräver en lagrad anslutningssträng som Funktioner använder för att komma åt den bundna tjänsten. Om du vill göra den här anslutningen enklare använder du det lagringskonto som du skapade med funktionsappen. Anslutningen till det här kontot lagras redan `AzureWebJobsStorage`i appinställningen .  

## <a name="prerequisites"></a>Krav

Innan du börjar den här artikeln, slutföra stegen i [del 1 av Java snabbstart](functions-create-first-java-maven.md).

## <a name="download-the-function-app-settings"></a>Ladda ned funktionsappinställningarna

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Aktivera tilläggspaket

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Nu kan du lägga till storage-utdatabindningen i projektet.

## <a name="add-an-output-binding"></a>Lägg till en utdatabindning

I ett Java-projekt definieras bindningarna som bindande anteckningar på funktionsmetoden. *Filen function.json* skapas sedan automatiskt baserat på dessa anteckningar.

Bläddra till platsen för din _funktionskod under src/main/java_, öppna *function.java-projektfilen* och lägg till följande parameter i metoddefinitionen: `run`

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

Parametern `msg` är [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) en typ som representerar en samling strängar som skrivs som meddelanden till en utdatabindning när funktionen är klar. I det här fallet är utdata en lagringskö med namnet `outqueue`. Anslutningssträngen för lagringskontot `connection` anges med metoden. I stället för själva anslutningssträngen skickar du programinställningen som innehåller anslutningssträngen för lagringskonto.

Metoddefinitionen `run` ska nu se ut som följande exempel:  

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

Nu kan du använda `msg` den nya parametern för att skriva till utdatabindningen från funktionskoden. Lägg till följande kodrad innan det lyckade `name` svaret `msg` för att lägga till värdet för utdatabindningen.

```java
msg.setValue(name);
```

När du använder en utdatabindning behöver du inte använda Azure Storage SDK-koden för autentisering, hämta en köreferens eller skriva data. Funktionskörnings- och köutdatabindningen utför dessa uppgifter åt dig.

Din `run` metod bör nu se ut som följande exempel:

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

Eftersom arketypen också skapar en uppsättning tester måste du uppdatera `msg` dessa tester `run` för att hantera den nya parametern i metodsignaturen.  

Bläddra till platsen för testkoden under _src/test/java_, öppna *function.java-projektfilen* `//Invoke` och ersätt kodraden under med följande kod.

```java
@SuppressWarnings("unchecked")
final OutputBinding<String> msg = (OutputBinding<String>)mock(OutputBinding.class);

// Invoke
final HttpResponseMessage ret = new Function().run(req, msg, context);
``` 

Du är nu redo att prova den nya utdatabindningen lokalt.

## <a name="run-the-function-locally"></a>Kör funktionen lokalt

Som tidigare använder du följande kommando för att skapa projektet och starta funktionerskörningen lokalt:

::: zone pivot="java-build-tools-maven"  
```bash
mvn clean package 
mvn azure-functions:run
```
::: zone-end

::: zone pivot="java-build-tools-gradle"  
```bash
gradle jar --info
gradle azureFunctionsRun
```
::: zone-end

> [!NOTE]  
> Eftersom du har aktiverat tilläggspaket i host.json hämtades och installerades [tillägget Lagringsbindning](functions-bindings-storage-blob.md#add-to-your-functions-app) åt dig under start, tillsammans med de andra Microsoft-bindningstilläggen.

Precis som tidigare utlöser du funktionen från kommandoraden med cURL i ett nytt terminalfönster:

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

Den här gången skapar utdatabindningen också en kö som namnges `outqueue` i ditt Lagringskonto och lägger till ett meddelande med samma sträng.

Därefter använder du Azure CLI för att visa den nya kön och verifiera att ett meddelande har lagts till. Du kan också visa din kö med hjälp av [Microsoft Azure Storage Explorer][Azure Storage Explorer] eller i [Azure-portalen](https://portal.azure.com).

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Omfördela projektet 

Om du vill uppdatera den publicerade appen kör du följande kommando igen:  

::: zone pivot="java-build-tools-maven"  
```bash
mvn azure-functions:deploy
```
::: zone-end

::: zone pivot="java-build-tools-gradle"  
```bash
gradle azureFunctionsDeploy
```
::: zone-end

Återigen kan du använda cURL för att testa den distribuerade funktionen. Precis som tidigare `AzureFunctions` skickar du värdet i brödtexten i POST-begäran till webbadressen, som i det här exemplet:

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Du kan [granska meddelandet Lagringskö](#query-the-storage-queue) igen för att kontrollera att utdatabindningen genererar ett nytt meddelande i kön, som förväntat.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

Du har uppdaterat funktionen HTTP för att skriva data till en lagringskö. Mer information om hur du utvecklar Azure Functions med Java finns i [Azure Functions Java-utvecklarguiden](functions-reference-java.md) och [Azure Functions-utlösare och bindningar](functions-triggers-bindings.md). Exempel på kompletta funktionsprojekt i Java finns i [javafunktionsexempelen](/samples/browse/?products=azure-functions&languages=Java). 

Därefter bör du aktivera Application Insights-övervakning för din funktionsapp:

> [!div class="nextstepaction"]
> [Aktivera Application Insights-integrering](functions-monitoring.md#manually-connect-an-app-insights-resource)


[Azure Storage Explorer]: https://storageexplorer.com/
