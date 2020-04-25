---
title: Referens för Java-utvecklare för Azure Functions
description: Lär dig hur du utvecklar funktioner med Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 19a290fe7717d7838e8fcd1d1f5cddb3f54eb812
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82145337"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java Developer Guide

Azure Functions runtime stöder [Java se 8 LTS (Zulu 8.31.0.2-JRE 8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Den här guiden innehåller information om erna för att skriva Azure Functions med Java.

När det sker på andra språk kan en Funktionsapp ha en eller flera funktioner. En Java-funktion är `public` en metod, dekorerad med anteckningen `@FunctionName`. Den här metoden definierar posten för en Java-funktion och måste vara unik i ett visst paket. En Funktionsapp som skrivits i Java kan ha flera klasser med flera offentliga metoder som är `@FunctionName`kommenterade med.

Den här artikeln förutsätter att du redan har läst [Azure Functions Developer-referensen](functions-reference.md). Du bör också slutföra snabb starten för funktioner för att skapa din första funktion med hjälp av [Visual Studio Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java) eller [maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java).

## <a name="programming-model"></a>Programmeringsmodell 

Begreppen [utlösare och bindningar](functions-triggers-bindings.md) är grundläggande för Azure Functions. Utlösare startar körningen av koden. Bindningar ger dig ett sätt att skicka data till och returnera data från en funktion utan att behöva skriva anpassad kod för data åtkomst.

## <a name="create-java-functions"></a>Skapa Java-funktioner

För att göra det enklare att skapa Java-funktioner finns det maven verktyg och archetypes som använder fördefinierade Java-mallar för att skapa projekt med en speciell funktions utlösare.    

### <a name="maven-based-tooling"></a>Maven verktyg

Följande utvecklings miljöer har Azure Functions verktyg som du kan använda för att skapa Java-funktions projekt: 

+ [Visual Studio-koden](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Sol](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

I artikel länkarna ovan visas hur du skapar dina första funktioner med hjälp av en valfri IDE. 

### <a name="project-scaffolding"></a>Project-ramverk

Om du föredrar kommando rads utveckling från terminalen är det enklaste sättet att Autogenerera Java-baserade funktions projekt att använda `Apache Maven` archetypes. Java maven-archetype för Azure Functions publiceras _i följande_:_artifactId_: [com. Microsoft. Azure: Azure-Functions-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/). 

Följande kommando genererar ett nytt Java-funktions projekt med hjälp av den här archetype:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

För att komma igång med den här archetype, se [Java-snabb](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java)starten. 

## <a name="create-kotlin-functions-preview"></a>Skapa Kotlin-funktioner (förhands granskning)

Det finns också en maven-archetype för att skapa Kotlin-funktioner. Den här archetype, som för närvarande finns i för hands version, publiceras _under följande__artifactId_: [com. Microsoft. Azure: Azure-Functions-Kotlin-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/). 

Följande kommando genererar ett nytt Java-funktions projekt med hjälp av den här archetype:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

För att komma igång med den här archetype, se snabb starten för [Kotlin](functions-create-first-kotlin-maven.md).

## <a name="folder-structure"></a>Mappstruktur

Här är mappstrukturen för ett Azure Functions Java-projekt:

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

_* Kotlin-projektet ser likadant ut eftersom det fortfarande är maven_

Du kan använda en delad [Host. JSON](functions-host-json.md) -fil för att konfigurera Function-appen. Varje funktion har sin egen kod fil (. Java) och bindnings konfigurations fil (Function. JSON).

Du kan använda mer än en funktion i ett projekt. Undvik att placera dina funktioner i separata jar v7. `FunctionApp` I mål katalogen är vad som distribueras till din Function-app i Azure.

## <a name="triggers-and-annotations"></a>Utlösare och anteckningar

 Funktioner anropas av en utlösare, till exempel en HTTP-begäran, en timer eller en uppdatering av data. Din funktion måste bearbeta den utlösaren och alla andra indata för att skapa en eller flera utdata.

Använd de Java-anteckningar som ingår i [com. Microsoft. Azure. Azure. functions. Annotation. *-](/java/api/com.microsoft.azure.functions.annotation) paketet för att binda indata och utdata till dina metoder. Mer information finns i [referens dokumenten för Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Du måste konfigurera ett Azure Storage konto i din [lokala. Settings. JSON](/azure/azure-functions/functions-run-local#local-settings-file) för att köra Azure Blob Storage, Azure Queue Storage eller Azure Table Storage-utlösare lokalt.

Exempel:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Här är det genererade `function.json` som motsvarar [Azure-Functions-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="jdk-runtime-availability-and-support"></a>Tillgänglighet och support för JDK-körning 

För lokal utveckling av Java Functions-appar laddar du ned och använder [Azul Zulu Enterprise för Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDKs från [Azul-system](https://www.azul.com/downloads/azure-only/zulu/). Azure Functions använder Azul Java 8 JDK runtime när du distribuerar dina funktions program till molnet.

[Azure-support](https://azure.microsoft.com/support/) för problem med JDKs-och Function-appar är tillgänglig med ett [kvalificerat support](https://azure.microsoft.com/support/plans/)avtal.

## <a name="customize-jvm"></a>Anpassa JVM

Med funktioner kan du anpassa den Java Virtual Machine (JVM) som används för att köra Java-funktioner. [Följande JVM-alternativ](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) används som standard:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Du kan ange ytterligare argument i en app-inställning `JAVA_OPTS`med namnet. Du kan lägga till appinställningar till din Function-app distribuerad till Azure i Azure Portal eller Azure CLI.

> [!IMPORTANT]  
> I förbruknings planen måste du också lägga till inställningen WEBSITE_USE_PLACEHOLDER med värdet 0 för att anpassningen ska fungera. Den här inställningen ökar kall start tider för Java functions.

### <a name="azure-portal"></a>Azure Portal

I [Azure Portal](https://portal.azure.com)använder du [fliken program inställningar](functions-how-to-use-azure-function-app-settings.md#settings) för att lägga till `JAVA_OPTS` inställningen.

### <a name="azure-cli"></a>Azure CLI

Du kan använda kommandot [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) för att ange `JAVA_OPTS`, som i följande exempel:

#### <a name="consumption-plan"></a>[Förbruknings plan](#tab/consumption)
```azurecli-interactive
az functionapp config appsettings set \
--settings "JAVA_OPTS=-Djava.awt.headless=true" \
"WEBSITE_USE_PLACEHOLDER=0" \
--name <APP_NAME> --resource-group <RESOURCE_GROUP>
```
#### <a name="dedicated-plan--premium-plan"></a>[Dedikerad plan/Premium-plan](#tab/dedicated+premium)
```azurecli-interactive
az functionapp config appsettings set \
--settings "JAVA_OPTS=-Djava.awt.headless=true" \
--name <APP_NAME> --resource-group <RESOURCE_GROUP>
```
---

Det här exemplet aktiverar konsol löst läge. Ersätt `<APP_NAME>` med namnet på din Function-app och `<RESOURCE_GROUP>` med resurs gruppen. 

## <a name="third-party-libraries"></a>Bibliotek från tredje part 

Azure Functions stöder användningen av bibliotek från tredje part. Som standard paketeras alla beroenden som anges `pom.xml` i projekt filen automatiskt under [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) målet. För bibliotek som inte har angetts som beroenden i `pom.xml` filen placerar du dem `lib` i en katalog i funktionens rot Katalog. Beroenden som placeras `lib` i katalogen läggs till i system klass inläsaren vid körning.

`com.microsoft.azure.functions:azure-functions-java-library` Beroendet anges i classpath som standard och behöver inte tas med i `lib` katalogen. [Azure-Functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) lägger också till beroenden som anges [här](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) i classpath.

## <a name="data-type-support"></a>Data typs stöd

Du kan använda vanliga gamla Java-objekt (Pojo), typer som `azure-functions-java-library`definieras i eller primitiva data typer, till exempel sträng och heltal som ska bindas till indata-eller utgående bindningar.

### <a name="pojos"></a>Pojo

För att konvertera indata till POJO, använder [Azure-Functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) [Gson](https://github.com/google/gson) -biblioteket. POJO-typer som används som indata till `public`funktioner ska vara.

### <a name="binary-data"></a>Binära data

Binda binära indata eller utdata `byte[]`till, genom att `dataType` ange fältet i function. JSON till `binary`:

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Om du förväntar dig null `Optional<T>`-värden använder du.

## <a name="bindings"></a>Bindningar

Bindningar för indata och utdata ger ett deklarativ sätt att ansluta till data i din kod. En funktion kan ha flera bindningar för indata och utdata.

### <a name="input-binding-example"></a>Exempel på indatamängds bindning

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

Du anropar den här funktionen med en HTTP-begäran. 
- HTTP-begärans nytto Last skickas `String` som en för `inputReq`argumentet.
- En post hämtas från Table Storage och skickas som `TestInputData` argumentet. `inputData`

Om du vill ta emot en batch med indata kan `String[]`du `POJO[]`binda `List<String>`till, `List<POJO>`, eller.

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

Den här funktionen utlöses när det finns nya data i den konfigurerade händelsehubben. Eftersom `cardinality` är inställt `MANY`på, tar funktionen emot en batch med meddelanden från händelsehubben. `EventData`från Event Hub konverteras till `TestEventData` för att köra funktionen.

### <a name="output-binding-example"></a>Exempel på utgående bindning

Du kan binda en utgående bindning till returvärdet med hjälp `$return`av. 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Om det finns flera utgående bindningar använder du returvärdet för bara en av dem.

Om du vill skicka flera utmatnings värden `OutputBinding<T>` använder `azure-functions-java-library` du definierade i paketet. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

Du anropar den här funktionen på en HttpRequest. Den skriver flera värden till Queue Storage.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage och HttpResponseMessage

 Dessa definieras i `azure-functions-java-library`. De är hjälp typer för att arbeta med HttpTrigger-funktioner.

| Specialiserad typ      |       Mål        | Typisk användning                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP-utlösare     | Hämtar metod, rubriker eller frågor |
| `HttpResponseMessage` | HTTP-utgående bindning | Returnerar annan status än 200   |

## <a name="metadata"></a>Metadata

Några utlösare skickar [Utlös ande metadata](/azure/azure-functions/functions-triggers-bindings) tillsammans med indata. Du kan använda anteckningen `@BindingName` för att binda till Utlös ande metadata.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
I föregående exempel `queryValue` är bindningen till FRÅGESTRÄNGPARAMETERN `name` i URL: en för http-begäran. `http://{example.host}/api/metadata?name=test` Här är ett annat exempel som visar hur du binder `Id` till från kö-utlösarens metadata.

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> Namnet som anges i anteckningen måste matcha metadata-egenskapen.

## <a name="execution-context"></a>Körningskontext

`ExecutionContext`, som definieras i `azure-functions-java-library`, innehåller hjälp metoder för att kommunicera med Functions-körningen.

### <a name="logger"></a>Loggar

Använd `getLogger`, definierad i `ExecutionContext`, för att skriva loggar från funktions kod.

Exempel:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Visa loggar och spåra

Du kan använda Azure CLI för att strömma Java STDOUT-och stderr-loggning, samt annan program loggning. 

Så här konfigurerar du din Function-app för att skriva program loggning med hjälp av Azure CLI:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Om du vill strömma logga utdata för din Function-app med hjälp av Azure CLI öppnar du en ny kommando tolk, bash eller terminalserversession och anger följande kommando:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
Kommandot [AZ webapp log pilslut](/cli/azure/webapp/log) har alternativ för att filtrera utdata med hjälp av `--provider` alternativet. 

Om du vill hämta loggfilerna som en enda ZIP-fil med hjälp av Azure CLI öppnar du en ny kommando tolk, bash eller terminalserversession och anger följande kommando:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Du måste ha aktiverat fil system loggning i Azure Portal eller Azure CLI innan du kör det här kommandot.

## <a name="environment-variables"></a>Miljövariabler

I funktioner visas [appinställningar](functions-app-settings.md), till exempel tjänst anslutnings strängar, som miljövariabler under körningen. Du kan komma åt de här inställningarna med `System.getenv("AzureWebJobsStorage")`hjälp av.

I följande exempel hämtas [program inställningen](functions-how-to-use-azure-function-app-settings.md#settings)med nyckeln med namnet `myAppSetting`:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

> [!NOTE]
> Värdet för AppSetting FUNCTIONS_EXTENSION_VERSION ska vara ~ 2 eller ~ 3 för en optimerad kall start upplevelse.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Functions Java-utveckling finns i följande resurser:

* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)
* [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)
* Lokal utveckling och fel sökning med [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md)och [Sol förmörkelse](functions-create-maven-eclipse.md)
* [Fjärrfelsökning Java Azure Functions med Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven-plugin-program för Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Effektivisera skapandet av funktioner genom `azure-functions:add` målet och Förbered en mellanlagringsplats för distribution av [zip-filer](deployment-zip-push.md).
