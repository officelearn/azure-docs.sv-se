---
title: Java-utvecklare för Azure Functions | Microsoft Docs
description: Förstå hur du utvecklar funktioner med Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure functions, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur och java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: routlaw
ms.openlocfilehash: 65964372cf2a0aa42be967f7c93749c58a9f56dd
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621777"
---
# <a name="azure-functions-java-developer-guide"></a>Utvecklarguide för Azure Functions Java

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

## <a name="programming-model"></a>Programmeringsmodell 

Din Azure-funktion ska vara en tillståndslös klassmetod som bearbetar indata och utdata. Även om du ska kunna skriva instans metoder, måste din funktion inte beror på eventuella Instansfält i klassen. Alla metoder för funktionen måste ha en `public` Åtkomstmodifieraren.

## <a name="triggers-and-annotations"></a>Utlösare och anteckningar

En Azure-funktion anropas vanligtvis på grund av en extern utlösare. Din funktion måste bearbeta den utlösaren och dess associerade indata och framställer en eller flera utdata.

Java-anteckningar som ingår i den `azure-functions-java-core` paket att binda indata och utdata till din metoder. Stöds inkommande utlösare och anteckningar-utdatabindning som ingår i följande tabell:

Bindning | Anteckning
---|---
CosmosDB | Gäller inte
HTTP | <ul><li>`HttpTrigger`</li><li>`HttpOutput`</li></ul>
Mobile Apps | Gäller inte
Notification Hubs | Gäller inte
Storage Blob | <ul><li>`BlobTrigger`</li><li>`BlobInput`</li><li>`BlobOutput`</li></ul>
Lagringskö | <ul><li>`QueueTrigger`</li><li>`QueueOutput`</li></ul>
Storage-tabell | <ul><li>`TableInput`</li><li>`TableOutput`</li></ul>
Timer | <ul><li>`TimerTrigger`</li></ul>
Twilio | Gäller inte

Utlösarens indata och utdata kan också definieras i den [function.json](/azure/azure-functions/functions-reference#function-code) för ditt program.

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

Du kan använda alla datatyper i Java för inkommande och utgående data, inklusive inbyggda typer. anpassad Java-typer och specialiserade Azure typer som definieras i `azure-functions-java-core` paketet. I Azure Functions runtime försöker konvertera indata till i den typ som begärdes av din kod.

### <a name="strings"></a>Strängar

Värden som skickas till funktionen metoder att konverteras till strängar om motsvarande typ av indataparameter för funktionen är av typen `String`. 

### <a name="plain-old-java-objects-pojos"></a>Vanlig gamla Java objects (Pojo)

Strängar med JSON-formaterad att konverteras till Java-typer om indata för metoden funktionen förväntar sig att Java-datatypen. Den här konverteringen kan du skicka JSON-indata till dina funktioner och arbeta med Java-typer i din kod utan att behöva implementera konverteringen i din egen kod.

POJO-typer som används som indata till funktioner måste samma `public` Åtkomstmodifieraren som funktionen-metoder som de som används i. Du inte deklarera POJO klass fält `public`. Till exempel en JSON-sträng `{ "x": 3 }` kan konverteras till typen följande POJO:

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>Binära data

Binära data representeras som en `byte[]` i Azure functions-kod. Binda binära indata eller utdata till dina funktioner genom att ange den `dataType` i din function.json till `binary`:

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

Använd `OutputBinding<byte[]>` Skriv för att göra en binär utdatabindning.


## <a name="function-method-overloading"></a>Funktionen metoden överbelastning

Du kan överbelasta funktionen metoder med samma namn men med olika typer. Du kan till exempel ha både `String echo(String s)` och `String echo(MyType s)` i en klass och Azure Functions runtime avgör vilken som ska anropa genom att granska den faktiska indatatypen (för HTTP-indata, MIME-typ `text/plain` leder till `String` medan `application/json` representerar `MyType`).

## <a name="inputs"></a>Indata

Indata är indelade i två kategorier i Azure Functions: en är indata för arbetsflödesutlösaren och den andra är ytterligare indata. Även om de skiljer sig i `function.json`, användningen är identiska i Java-kod. Låt oss ta ett kodfragment som exempel:

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

Den `@BindingName` anteckning accepterar en `String` egenskap som representerar namnet för bindning/utlösaren som definierats i `function.json`:

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

Så när den här funktionen anropas nyttolasten i HTTP-begäran skickar en valfri `String` för argumentet `in` och en Azure-tabellagring `MyObject` typ angavs för argumentet `obj`. Använd den `Optional<T>` Skriv för att hantera indata i dina funktioner som kan vara null.

## <a name="outputs"></a>Utdata

Utdata kan uttryckas både i returvärdet eller utdataparametrar. Om det finns bara en utdata, rekommenderas du att använda det returnera värdet. Du måste använda utdataparametrar för flera utdata.

Returvärdet är den enklaste formen av utdata du returnera bara värdet av valfri typ och Azure Functions runtime kommer försök att konvertera tillbaka till den faktiska typ (till exempel ett HTTP-svar). I `functions.json`, du använder `$return` som namnet på utdata-bindning.

För att skapa flera utdatavärden använder `OutputBinding<T>` typen som definierats i den `azure-functions-java-core` paketet. Om du vill göra ett HTTP-svar och skickar ett meddelande till en kö och kan du skriva något som liknar:

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

som bör definiera utdatabindning i `function.json`:

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
## <a name="specialized-types"></a>Specialtyper

En funktion måste ibland har detaljerad kontroll över indata och utdata. Specialiserade typer i den `azure-functions-java-core` paketet tillhandahålls åt dig att hantera om begäraninformation och anpassa returstatus för en HTTP-utlösare:

| Specialiserad typ      |       Mål        | Normal användning                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP-utlösare     | Hämta-metoden, sidhuvuden eller frågor |
| `HttpResponseMessage<T>` | HTTP-Utdatabindning | Returnera status än 200   |

> [!NOTE] 
> Du kan också använda `@BindingName` anteckning att hämta HTTP-huvuden och frågor. Till exempel `@BindingName("name") String query` upprepas över HTTP-begärans sidhuvud och frågor och skicka detta värde till metoden. Till exempel `query` blir `"test"` om begäran-URL: en är `http://example.org/api/echo?name=test`.

### <a name="metadata"></a>Metadata

Metadata kommer från olika källor, som HTTP-huvuden, HTTP-frågor och [utlösa metadata](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties). Använd den `@BindingName` anteckning tillsammans med Metadatanamn för att hämta värdet.

Till exempel den `queryValue` i följande kod kodavsnitt kommer att `"test"` om den begärda Webbadressen är `http://{example.host}/api/metadata?name=test`.

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

## <a name="functions-execution-context"></a>Functions körningskontext

Du kan interagera med Azure Functions-körningsmiljö via den `ExecutionContext` objekt som definieras i den `azure-functions-java-core` paketet. Använd den `ExecutionContext` objekt som ska användas anrop information och functions runtime i din kod.

### <a name="logging"></a>Loggning

Åtkomst till Functions runtime loggaren är tillgänglig via den `ExecutionContext` objekt. Den här loggaren är knutna till Azure monitor och du kan ange flaggan varningar och fel påträffades under körning av funktion.

Följande exempelkod loggar en varning när begärandetexten emot är tom.

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

Ofta är det klokt att extrahera hemlig information från källkoden av säkerhetsskäl. På så sätt kan kod publiceras till källan koddatabaser utan att ange autentiseringsuppgifter att andra utvecklare av misstag. Detta kan uppnås genom att använda miljövariabler, både när du kör Azure Functions lokalt och när du distribuerar dina funktioner till Azure.

För att enkelt ställa in miljövariabler när du kör Azure Functions lokalt, kan du lägga till dessa variabler i filen local.settings.json. Om en inte finns i rotkatalogen för din function-projekt, passa på att skapa en. Här är hur filen ska se ut:

```xml
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "AzureWebJobsDashboard": ""
  }
}
```

Var och en nyckel / värde-mappning i den `values` kartan ska göras tillgänglig vid körning som en miljövariabel tillgänglig genom att anropa `System.getenv("<keyname>")`, till exempel `System.getenv("AzureWebJobsStorage")`. Lägga till ytterligare nyckel / värde-par accepteras och rekommenderar.

> [!NOTE]
> Om den här metoden är upptaget vara säker att tänka på om att lägga till local.settings.json filen till din lagringsplats Ignorera fil, så att den inte har allokerats.

Med din kod nu beroende på de här miljövariablerna kan du logga in på Azure Portal för att ange samma nyckel / värde-par i din funktionsappinställningar så att din kod equivalently fungerar när du testar lokalt och när du har distribuerat på Azure.

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser:

* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)
* [Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)
* [Fjärrfelsök Java Azure Functions med Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
