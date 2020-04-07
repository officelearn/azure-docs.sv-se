---
title: Java-utvecklarreferens för Azure-funktioner
description: Förstå hur man utvecklar funktioner med Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 4b1f39ff4fd48a3ed99b34391e9cc6efdad86a5d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673006"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java-utvecklarguide

Azure Functions-körningen stöder [Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64).](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/) Den här guiden innehåller information om krångligheter att skriva Azure Functions med Java.

När det händer andra språk kan en funktionsapp ha en eller flera funktioner. En Java-funktion `public` är en metod som `@FunctionName`är dekorerad med anteckningen . Den här metoden definierar posten för en Java-funktion och måste vara unik i ett visst paket. En funktionsapp skriven i Java kan ha flera `@FunctionName`klasser med flera offentliga metoder kommenterade med .

Den här artikeln förutsätter att du redan har läst [Utvecklarreferensen för Azure Functions](functions-reference.md). Du bör också slutföra snabbstarten Funktioner för att skapa din första funktion, med hjälp av [Visual Studio Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java) eller [Maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java).

## <a name="programming-model"></a>Programmeringsmodell 

Begreppen [utlösare och bindningar](functions-triggers-bindings.md) är grundläggande för Azure-funktioner. Utlösare startar körningen av koden. Bindningar ger dig ett sätt att skicka data till och returnera data från en funktion, utan att behöva skriva anpassad dataåtkomstkod.

## <a name="create-java-functions"></a>Skapa Java-funktioner

För att göra det enklare att skapa Java-funktioner finns maven-baserade verktyg och arketyper som använder fördefinierade Java-mallar som hjälper dig att skapa projekt med en specifik funktionsutlösare.    

### <a name="maven-based-tooling"></a>Maven-baserade verktyg

Följande utvecklarmiljöer har Azure Functions-verktyg som gör att du kan skapa Java-funktionsprojekt: 

+ [Visual Studio-koden](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

Artikeln länkar ovan visar hur du skapar dina första funktioner med din IDE val. 

### <a name="project-scaffolding"></a>Projekt byggnadsställningar

Om du föredrar kommandoradsutveckling från terminalen är det enklaste sättet att `Apache Maven` byggnadsställning Java-baserade funktionsprojekt att använda arketyper. Java Maven-arketypen för Azure-funktioner publiceras under följande _groupId_:_artifactId_: [com.microsoft.azure:azure-functions-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/). 

Följande kommando genererar ett nytt Java-funktionsprojekt med den här arketypen:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

Mer om du vill komma igång med den här arketypen finns i [snabbstarten för Java](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java). 

## <a name="create-kotlin-functions-preview"></a>Skapa Kotlin-funktioner (förhandsgranskning)

Det finns också en Maven arketyp för att generera Kotlin funktioner. Den här arketypen, som för närvarande är i förhandsversion, publiceras under följande _groupId_:_artifactId_: [com.microsoft.azure:azure-functions-kotlin-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/). 

Följande kommando genererar ett nytt Java-funktionsprojekt med den här arketypen:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

För att komma igång med den här arketypen, se [Kotlin snabbstart](functions-create-first-kotlin-maven.md).

## <a name="folder-structure"></a>Mappstrukturen

Här är mappstrukturen för ett Java-projekt för Azure Functions:

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

_* Kotlin projektet ser mycket lika eftersom det fortfarande är Maven_

Du kan använda en delad [host.json-fil](functions-host-json.md) för att konfigurera funktionsappen. Varje funktion har sin egen kodfil (.java) och bindning konfigurationsfil (function.json).

Du kan placera mer än en funktion i ett projekt. Undvik att sätta dina funktioner i separata burkar. I `FunctionApp` målkatalogen är det som distribueras till din funktionsapp i Azure.

## <a name="triggers-and-annotations"></a>Utlösare och anteckningar

 Funktioner anropas av en utlösare, till exempel en HTTP-begäran, en timer eller en uppdatering av data. Din funktion måste bearbeta utlösaren och alla andra indata för att producera en eller flera utdata.

Använd Java-anteckningarna som ingår i paketet [com.microsoft.azure.functions.annoteation.*](/java/api/com.microsoft.azure.functions.annotation) för att binda indata och utdata till dina metoder. Mer information finns i [Java-referensdokumenten](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Du måste konfigurera ett Azure Storage-konto i din [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) för att köra Azure Blob storage, Azure Queue storage eller Azure Table storage-utlösare lokalt.

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

Här är den `function.json` genererade motsvarande av [azure-funktioner-maven-plugin:](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin)

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

## <a name="jdk-runtime-availability-and-support"></a>JDK-tillgänglighet och support för körning 

För lokal utveckling av Java-funktionsappar kan du hämta och använda [Azul Zulu Enterprise för Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDK:er från [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/). Azure Functions använder Azul Java 8 JDK-körningen när du distribuerar dina funktionsappar till molnet.

[Azure-stöd](https://azure.microsoft.com/support/) för problem med JDK:erna och funktionsapparna är tillgängligt med en [kvalificerad supportplan](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>Anpassa JVM

Med funktioner kan du anpassa den virtuella Java-maskin (JVM) som används för att köra dina Java-funktioner. [Följande JVM-alternativ](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) används som standard:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Du kan ange ytterligare argument `JAVA_OPTS`i en appinställning med namnet . Du kan lägga till appinställningar i din funktionsapp som distribueras till Azure i Azure-portalen eller Azure CLI.

### <a name="azure-portal"></a>Azure Portal

I [Azure-portalen](https://portal.azure.com)använder du fliken `JAVA_OPTS` [Programinställningar](functions-how-to-use-azure-function-app-settings.md#settings) för att lägga till inställningen.

### <a name="azure-cli"></a>Azure CLI

Du kan använda kommandot [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) för att ange `JAVA_OPTS`, som i följande exempel:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
Det här exemplet aktiverar huvudlöst läge. Ersätt `<APP_NAME>` med namnet på funktionsappen och `<RESOURCE_GROUP>` med resursgruppen.

> [!WARNING]  
> I [förbrukningsplanen](functions-scale.md#consumption-plan)måste du `WEBSITE_USE_PLACEHOLDER` lägga till `0`inställningen med värdet .  
Den här inställningen ökar kallstartstiderna för Java-funktioner.

## <a name="third-party-libraries"></a>Bibliotek från tredje part 

Azure Functions stöder användning av bibliotek från tredje part. Som standard buntas alla beroenden som anges i `pom.xml` [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) projektfilen automatiskt under målet. För bibliotek som inte anges som `pom.xml` beroenden i `lib` filen placerar du dem i en katalog i funktionens rotkatalog. Beroenden som `lib` placeras i katalogen läggs till i systemklasslastaren vid körning.

Beroendet `com.microsoft.azure.functions:azure-functions-java-library` tillhandahålls som standard på klasssökvägen och behöver inte inkluderas i katalogen. `lib` Dessutom lägger [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) till beroenden som visas [här](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) i klasssökvägen.

## <a name="data-type-support"></a>Stöd för datatyp

Du kan använda Oas gamla Java-objekt `azure-functions-java-library`(POJOs), typer som definierats i eller primitiva datatyper som Sträng och Heltal för att binda till indata- eller utdatabindningar.

### <a name="pojos"></a>PojOs

För att konvertera indata till POJO använder [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) [gson-biblioteket.](https://github.com/google/gson) POJO-typer som används som `public`indata till funktioner bör vara .

### <a name="binary-data"></a>Binära data

Bind binära indata `byte[]`eller utdata `dataType` till , genom att `binary`ställa in fältet i din function.json till :

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

Om du förväntar dig `Optional<T>`null-värden använder du .

## <a name="bindings"></a>Bindningar

Indata- och utdatabindningar är ett deklarativt sätt att ansluta till data inifrån koden. En funktion kan ha flera in- och utdatabindningar.

### <a name="input-binding-example"></a>Exempel på indatabindning

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
- HTTP-begäran nyttolast `String` skickas som `inputReq`en för argumentet .
- En post hämtas från Tabelllagring och `TestInputData` skickas `inputData`som till argumentet .

Om du vill ta emot en `String[]`batch `POJO[]` `List<String>`med `List<POJO>`indata kan du binda till , , eller .

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

Den här funktionen utlöses när det finns nya data i den konfigurerade händelsehubben. Eftersom `cardinality` är inställd `MANY`på , tar funktionen emot en batch med meddelanden från händelsehubben. `EventData`från händelsehubben konverteras till `TestEventData` för funktionskörningen.

### <a name="output-binding-example"></a>Exempel på utdatabindning

Du kan binda en utdatabindning till returvärdet med hjälp `$return`av . 

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

Om det finns flera utdatabindningar använder du returvärdet för endast en av dem.

Om du vill skicka `OutputBinding<T>` flera `azure-functions-java-library` utdatavärden använder du definierat i paketet. 

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

Du anropar den här funktionen på en HttpRequest. Den skriver flera värden till Kölagring.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage och HttpResponseMessage

 Dessa definieras `azure-functions-java-library`i . De är hjälptyper för att arbeta med HttpTrigger-funktioner.

| Specialiserad typ      |       Mål        | Typisk användning                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP-utlösare     | Hämtar metod, rubriker eller frågor |
| `HttpResponseMessage` | BINDNING AV HTTP-utdata | Returnerar annan status än 200   |

## <a name="metadata"></a>Metadata

Få utlösare skickar [utlösa metadata](/azure/azure-functions/functions-triggers-bindings) tillsammans med indata. Du kan använda `@BindingName` anteckningar för att binda för att utlösa metadata.


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
I föregående exempel `queryValue` är frågesträngparametern `name` bunden till frågesträngen i HTTP-begäran-URL:en. `http://{example.host}/api/metadata?name=test` Här är ett annat exempel som `Id` visar hur du binder till från köutlösare metadata.

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
> Namnet i anteckningen måste matcha egenskapen metadata.

## <a name="execution-context"></a>Körningskontext

`ExecutionContext`, som `azure-functions-java-library`definieras i , innehåller hjälpmetoder för att kommunicera med funktionernas körning.

### <a name="logger"></a>Logger

Använd `getLogger`, `ExecutionContext`definierad i , för att skriva loggar från funktionskod.

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

## <a name="view-logs-and-trace"></a>Visa loggar och spårning

Du kan använda Azure CLI för att strömma Java stdout och stderr loggning, liksom andra programloggning. 

Så här konfigurerar du din funktionsapp för att skriva programloggning med hjälp av Azure CLI:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Om du vill strömma loggningsutdata för din funktionsapp med hjälp av Azure CLI öppnar du en ny kommandotolk, Bash eller Terminal-session och anger följande kommando:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
Az [webapp log tail](/cli/azure/webapp/log) kommandot har alternativ `--provider` för att filtrera utdata med hjälp av alternativet. 

Om du vill hämta loggfilerna som en enda ZIP-fil med hjälp av Azure CLI öppnar du en ny kommandotolk, Bash eller Terminal-session och anger följande kommando:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Du måste ha aktiverat filsystemloggning i Azure-portalen eller Azure CLI innan du kör det här kommandot.

## <a name="environment-variables"></a>Miljövariabler

I Funktioner visas [appinställningar](functions-app-settings.md), till exempel tjänstanslutningssträngar, som miljövariabler under körningen. Du kan komma åt `System.getenv("AzureWebJobsStorage")`dessa inställningar med hjälp av .

I följande exempel får [programinställningen](functions-how-to-use-azure-function-app-settings.md#settings) `myAppSetting`med nyckeln :

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Nästa steg

Mer information om Java-utveckling för Azure Functions finns i följande resurser:

* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)
* [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)
* Lokal utveckling och felsökning med [Visual Studio-kod,](https://code.visualstudio.com/docs/java/java-azurefunctions) [IntelliJ](functions-create-maven-intellij.md)och [Eclipse](functions-create-maven-eclipse.md)
* [Fjärrfelsöka Java Azure-funktioner med Visual Studio-kod](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven plugin för Azure-funktioner](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Effektivisera funktionsskapandet `azure-functions:add` genom målet och förbered en mellanlagringskatalog för [DISTRIBUTION AV ZIP-filer](deployment-zip-push.md).
