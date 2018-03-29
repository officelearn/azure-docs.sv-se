---
title: Java-utvecklare för Azure Functions | Microsoft Docs
description: Förstå hur du utvecklar fungerar med Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure functions, funktioner, händelsebearbetning, webhooks, dynamiska beräkning, serverlösa arkitektur, java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: routlaw
ms.openlocfilehash: 71576e65d20d7e8cb7f5ff1c5f19c82439bb6807
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="azure-functions-java-developer-guide"></a>Utvecklarhandbok för Azure Functions Java
> [!div class="op_single_selector"]
[!INCLUDE [functions-selector-languages](../../includes/functions-selector-languages.md)]

## <a name="programming-model"></a>Programmeringsmodell 

Din Azure-funktion måste vara en tillståndslös klassmetod som bearbetar indata och utdata. Även om du ska kunna skriva Instansmetoder måste din funktion inte beroende av några Instansfält i klassen. Alla metoder i funktionen måste ha en `public` åtkomstmodifierare.

## <a name="triggers-and-annotations"></a>Utlösare och anteckningar

En Azure-funktion anropas vanligtvis på grund av en extern utlösare. Funktionen måste bearbeta denna utlösare och dess associerade indata och skapar en eller flera utdata.

Java-kommentarer ingår i den `azure-functions-java-core` paketet binda indata och utdata till din metoder. Utlösare för stöds indata och utdata bindning anteckningar ingår i följande tabell:

Bindning | Anteckning
---|---
CosmosDB | Gäller inte
HTTP | <ul><li>`HttpTrigger`</li><li>`HttpOutput`</li></ul>
Mobile Apps | Gäller inte
Notification Hubs | Gäller inte
Storage Blob | <ul><li>`BlobTrigger`</li><li>`BlobInput`</li><li>`BlobOutput`</li></ul>
Lagringskö | <ul><li>`QueueTrigger`</li><li>`QueueOutput`</li></ul>
Tabell för lagring | <ul><li>`TableInput`</li><li>`TableOutput`</li></ul>
Timer | <ul><li>`TimerTrigger`</li></ul>
Twilio | Gäller inte

Utlösaren indata och utdata kan också definieras i den [function.json](/azure/azure-functions/functions-reference#function-code) för ditt program.

> [!IMPORTANT] 
> Du måste konfigurera ett Azure Storage-konto i din [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) att köra Azure Storage Blob, kö eller tabell utlösare lokalt.

Exempel på användning av anteckningar:

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Samma funktion som skrivits utan kommentarer:

```java
package com.example;

public class MyClass {
    public static String echo(String in) {
        return in;
    }
}
```

med motsvarande `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
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

## <a name="data-types"></a>Datatyper

Du kan använda alla datatyper i Java för inkommande och utgående data, inklusive inbyggda typer. anpassad Java-typer och särskilda Azure typer som definieras i `azure-functions-java-core` paketet. Azure-funktioner runtime försöker konvertera indata till i den typen som begärts av din kod.

### <a name="strings"></a>Strängar

Värden som skickades till funktionen metoder att konverteras till strängar om typen motsvarande Indataparametern för funktionen är av typen `String`. 

### <a name="plain-old-java-objects-pojos"></a>Vanlig gamla Java objects (Pojo)

Strängar som formaterats med JSON att konverteras till Java-typer om indata för metoden funktionen förväntar sig att Java-datatypen. Den här konverteringen kan du skicka JSON-indata till dina funktioner och arbeta med Java-typer i koden utan att behöva implementera konverteringen i din kod.

POJO typer som används som indata till funktioner måste samma `public` Åtkomstmodifieraren som de som används i funktionen metoderna. Du måste inte deklarera POJO klassfält `public`. Till exempel en JSON-sträng `{ "x": 3 }` kan konverteras till typen följande POJO:

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>Binära data

Binära data representeras som en `byte[]` i Azure functions koden. Binda binära indata eller utdata till dina funktioner genom att ange den `dataType` i din function.json till `binary`:

```json
 {
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "blob",
      "name": "content",
      "direction": "in",
      "dataType": "binary",
      "path": "container/myfile.bin",
      "connection": "ExampleStorageAccount"
    },
  ]
}
```

Använd det sedan i Funktionskoden:

```java
// Class definition and imports are omitted here
public static String echoLength(byte[] content) {
}
```

Använd `OutputBinding<byte[]>` typ att göra en binär utdata-bindning.


## <a name="function-method-overloading"></a>Funktionen metoden överbelastning

Du får överlagra funktionen metoder med samma namn men med olika typer. Du kan till exempel ha både `String echo(String s)` och `String echo(MyType s)` i en klass och Azure Functions runtime bestämmer vilken de ska anropa genom att undersöka faktiska Indatatyp (för HTTP-indata, MIME-typ `text/plain` leder till `String` medan `application/json` representerar `MyType`).

## <a name="inputs"></a>Indata

Indata är indelade i två kategorier i Azure Functions: en är utlösaren indata och den andra ytterligare indata. Även om de skiljer sig i `function.json`, användningen är identiska i Java-kod. Låt oss ta följande kodavsnitt som ett exempel:

```java
package com.example;

import com.microsoft.azure.serverless.functions.annotation.BindingName;
import java.util.Optional;

public class MyClass {
    public static String echo(Optional<String> in, @BindingName("item") MyObject obj) {
        return "Hello, " + in.orElse("Azure") + " and " + obj.getKey() + ".";
    }

    private static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

Den `@BindingName` anteckningen accepterar en `String` egenskap som representerar namnet på bindningen/utlösare som definieras i `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "put" ],
      "route": "items/{id}"
    },
    {
      "type": "table",
      "name": "item",
      "direction": "in",
      "tableName": "items",
      "partitionKey": "Example",
      "rowKey": "{id}",
      "connection": "ExampleStorageAccount"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```

Så när den här funktionen anropas nyttolasten för HTTP-begäran skickas en valfri `String` för argumentet `in` och en Azure-tabellagring `MyObject` typ skickades till argumentet `obj`. Använd den `Optional<T>` typen för att hantera indata till dina funktioner som kan vara null.

## <a name="outputs"></a>Utdata

Utdata kan uttryckas både i returvärde eller utdataparametrar. Om det finns endast en utdata, rekommenderas du att använda det returnera värdet. Du måste använda utdataparametrar flera utdata.

Returnerade värdet är den enklaste formen av utdata, du returnera bara värdet av valfri typ och Azure Functions-runtime görs ett försök att konvertera tillbaka till den faktiska typen (till exempel ett HTTP-svar). I `functions.json`, du använder `$return` som namn på utdata-bindning.

För att skapa flera utdatavärden använda `OutputBinding<T>` typ som definierats i den `azure-functions-java-core` paketet. Om du behöver göra ett HTTP-svar och skicka ett meddelande till en kö och kan du skriva något som liknar:

```java
package com.example;

import com.microsoft.azure.serverless.functions.OutputBinding;
import com.microsoft.azure.serverless.functions.annotation.BindingName;

public class MyClass {
    public static String echo(String body, 
    @QueueOutput(queueName = "messages", connection = "AzureWebJobsStorage", name = "queue") OutputBinding<String> queue) {
        String result = "Hello, " + body + ".";
        queue.setValue(result);
        return result;
    }
}
```

som ska definiera utdata-bindning i `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "queue",
      "name": "queue",
      "direction": "out",
      "queueName": "messages",
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```
## <a name="specialized-types"></a>Särskilda typer

En funktion måste ibland har detaljerad kontroll över indata och utdata. Särskilda typer i den `azure-functions-java-core` paketet har angetts för att manipulera begäraninformation och anpassa returnera statusen för en HTTP-utlösare:

| Specialiserad typ      |       Mål        | Normal användning                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP-utlösare     | Hämta-metoden, sidhuvud eller frågor |
| `HttpResponseMessage<T>` | Utdata för HTTP-bindning | Returstatus än 200   |

> [!NOTE] 
> Du kan också använda `@BindingName` anteckningen att hämta HTTP-huvuden och frågor. Till exempel `@BindingName("name") String query` itererar HTTP-huvuden för begäran och frågor och skickar det värdet till metoden. Till exempel `query` blir `"test"` om URL: en för begäran är `http://example.org/api/echo?name=test`.

### <a name="metadata"></a>Metadata

Metadata som kommer från olika källor, t.ex. HTTP-huvuden, http-frågor och [utlösa metadata](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties). Använd den `@BindingName` anteckningen tillsammans med namnet som metadata ska hämta värdet.

Till exempel den `queryValue` i följande kod fragment blir `"test"` om begärd URL är `http://{example.host}/api/metadata?name=test`.

```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.serverless.functions.annotation.*;

public class MyClass {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```

## <a name="functions-execution-context"></a>Funktioner körningskontext

Du interagerar med Azure Functions körningsmiljö via den `ExecutionContext` objekt som definieras i den `azure-functions-java-core` paketet. Använd den `ExecutionContext` objekt som ska användas anrop och funktioner runtime information i koden.

### <a name="logging"></a>Loggning

Åtkomst till funktioner runtime loggaren är tillgänglig via den `ExecutionContext` objekt. Den här loggaren är knutna till övervakaren Azure och du flaggan varningar och fel som uppstod under körningen av funktionen.

Följande exempelkod loggar en varning visas när begärandetexten emot är tom.

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="environment-variables"></a>Miljövariabler

Det är ofta önskvärt att extrahera hemlig information från källkoden av säkerhetsskäl. Detta gör att kod kan publiceras till källan kod repor utan att tillhandahålla autentiseringsuppgifter till andra utvecklare av misstag. Detta kan uppnås genom att använda miljövariabler, både när du kör Azure Functions lokalt och när du distribuerar dina funktioner till Azure.

För att enkelt ange miljövariabler när du kör Azure Functions lokalt, kan du lägga till dessa variabler i local.settings.json-filen. Om en inte är tillgänglig i rotkatalogen för projektet funktionen passa på att skapa en. Här är hur filen ska se ut:

```xml
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "AzureWebJobsDashboard": ""
  }
}
```

Varje nyckel / värde mappning i den `values` kartan ska göras tillgänglig vid körning som en miljövariabel som nås genom att anropa `System.getenv("<keyname>")`, till exempel `System.getenv("AzureWebJobsStorage")`. Lägga till ytterligare nyckel / värde-par accepteras och rekommenderar.

> [!NOTE]
> Om den här metoden är upptaget att kontrollera för att överväga om att lägga till local.settings.json filen till din databas ignorera filen, så att den inte är allokerad.

Med koden nu beroende på de här miljövariablerna, kan du logga in på Azure-portalen och ange samma nyckel / värde-par i funktionen app-inställningar så att din kod equivalently fungerar när testningen lokalt och när du har distribuerat till Azure.

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser:

* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)
* [Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)
* [Fjärråtkomst Debug Java Azure Functions med Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
