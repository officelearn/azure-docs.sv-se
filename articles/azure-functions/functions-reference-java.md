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
ms.date: 08/10/2018
ms.author: routlaw
ms.openlocfilehash: bbc1c3426b52e71db84a988b39a1d76ac24b6168
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697019"
---
# <a name="azure-functions-java-developer-guide"></a>Utvecklarguide för Azure Functions Java

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

## <a name="programming-model"></a>Programmeringsmodell 

Din Azure-funktion ska vara en tillståndslös klassmetod som bearbetar indata och utdata. Även om du kan skriva instans metoder, måste din funktion inte beror på eventuella Instansfält i klassen. Alla metoder för funktionen måste ha en `public` Åtkomstmodifieraren.

Du kan placera mer än en funktion i ett projekt. Undvik att placera dina funktioner i separata JAR-filer.

## <a name="triggers-and-annotations"></a>Utlösare och anteckningar

 Azure functions anropas av en utlösare, till exempel en HTTP-begäran, en timer eller en uppdatering av data. Din funktion måste bearbeta som utlösare och alla andra indata för att producera en eller flera utdata.

Använda Java-anteckningar som ingår i den [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) paket att binda indata och utdata till din metoder. Exempelkod med hjälp av dina kommentarer är tillgänglig i den [Java referensdokument](/java/api/com.microsoft.azure.functions.annotation) för varje anteckning och i Azure Functions-bindning referensdokumentationen, till exempel det för [HTTP-utlösare](/azure/azure-functions/functions-bindings-http-webhook).

Utlösarens indata och utdata kan också definieras i den [function.json](/azure/azure-functions/functions-reference#function-code) för funktionen i stället för via anteckningar. Med hjälp av `function.json` i stället för kommentarer på så vis rekommenderas inte.

> [!IMPORTANT] 
> Du måste konfigurera ett Azure Storage-konto i din [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) att köra Azure Storage Blob, kö eller tabell utlösare lokalt.

Exempel på användning av anteckningar:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
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

## <a name="third-party-libraries"></a>Bibliotek från tredje part 

Azure Functions har stöd för användning av bibliotek från tredje part. Som standard alla beroenden som anges i ditt projekt `pom.xml` filen automatiskt ska ligga under den `mvn package` mål. För bibliotek som inte har angetts som beroenden i den `pom.xml` filen, placera dem i en `lib` katalogen i funktionens rotkatalog. Beroendena placeras i den `lib` directory kommer att läggas till klassinläsare system vid körning.

## <a name="data-type-support"></a>Stöd för datatypen

Du kan använda alla datatyper i Java för inkommande och utgående data, inklusive inbyggda typer. anpassad Java-typer och specialiserade Azure typer som definieras i `azure-functions-java-library` paketet. I Azure Functions runtime försöker konvertera indata till i den typ som begärdes av din kod.

### <a name="strings"></a>Strängar

Värden som skickas till funktionen metoder att konverteras till strängar om motsvarande typ av indataparameter för funktionen är av typen `String`. 

### <a name="plain-old-java-objects-pojos"></a>Vanlig gamla Java objects (Pojo)

Strängar med JSON-formaterad att konverteras till Java-typer om signaturen för indata för funktionen förväntar sig att Java-datatypen. Den här konverteringen kan du skicka in JSON och arbeta med Java-typer.

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

Tom indatavärden kan vara `null` som din functions-argument, men en rekommenderade sätt att hantera potentiella tomma värden är att använda `Optional<T>`.


## <a name="function-method-overloading"></a>Funktionen metoden överbelastning

Du kan överbelasta funktionen metoder med samma namn men med olika typer. Du kan till exempel ha både `String echo(String s)` och `String echo(MyType s)` i en klass. Azure Functions avgör vilken metod du vill anropa baserat på Indatatyp (för HTTP-indata, MIME-typ `text/plain` leder till `String` medan `application/json` representerar `MyType`).

## <a name="inputs"></a>Indata

Indata är indelade i två kategorier i Azure Functions: en är indata för arbetsflödesutlösaren och den andra är ytterligare indata. Även om de skiljer sig i `function.json`, användningen är identiska i Java-kod. Låt oss ta ett kodfragment som exempel:

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String in,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") MyObject obj
    ) {
        return "Hello, " + in + " and " + obj.getKey() + ".";
    }

    public static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

När den här funktionen har utlösts HTTP-begäran skickas till funktionen av `String in`. En post som ska hämtas från Azure Table Storage utifrån ID i URL: en väg och görs tillgängliga som `obj` i själva funktionen.

## <a name="outputs"></a>Utdata

Utdata kan uttryckas både i returvärdet eller utdataparametrar. Om det finns bara en utdata, rekommenderas du att använda det returnera värdet. Du måste använda utdataparametrar för flera utdata.

Returvärdet är den enklaste formen av utdata du returnera bara värdet av valfri typ och Azure Functions runtime kommer försök att konvertera tillbaka till den faktiska typ (till exempel ett HTTP-svar).  Du kan använda eventuella utdata anteckningar till funktionen-metoden (name-egenskapen för anteckningen måste vara $return) att definiera returvärdet utdata.

För att skapa flera utdatavärden använder `OutputBinding<T>` typen som definierats i den `azure-functions-java-library` paketet. Om du vill göra ett HTTP-svar och skickar ett meddelande till en kö och kan du skriva något som liknar:

Exempelvis kan en blob-innehåll kopieras funktionen definieras som följande kod. `@StorageAccount` anteckningen används här för att förhindra att duplicera för Anslutningsegenskapen för både `@BlobTrigger` och `@BlobOutput`.

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Använd `OutputBinding<byte[]`> att göra en binär effekt värde (för parametrar); Använd bara för returvärden `byte[]`.

## <a name="specialized-types"></a>Specialtyper

En funktion måste ibland har detaljerad kontroll över indata och utdata. Specialiserade typer i den `azure-functions-java-core` paketet tillhandahålls åt dig att hantera om begäraninformation och anpassa returstatus av en HTTP-utlösare:

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
import com.microsoft.azure.functions.annotation.*;


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

## <a name="execution-context"></a>Körningskontext

Interagera med Azure Functions-körningsmiljö via den `ExecutionContext` objekt som definieras i den `azure-functions-java-library` paketet. Använd den `ExecutionContext` objekt som ska användas anrop information och functions runtime i din kod.

### <a name="custom-logging"></a>Anpassad loggning

Åtkomst till Functions runtime loggaren är tillgänglig via den `ExecutionContext` objekt. Den här loggaren är knutna till Azure monitor och du kan ange flaggan varningar och fel påträffades under körning av funktion.

Följande exempelkod loggar en varning när begärandetexten emot är tom.

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

Du kan använda Azure CLI för att stream Java som standard ut och felloggning samt andra programloggning. Först konfigurera funktionen programmet för att skriva programloggning med hjälp av Azure-CLI:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Strömma loggning utdata för din funktionsapp med hjälp av Azure CLI, öppna en kommandotolk, Bash eller terminalsession och ange följande kommando:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
Den [az webapp log tail](/cli/azure/webapp/log) kommandot har alternativ för att filtrera utdata med den `--provider` alternativet. 

Öppna en kommandotolk, Bash eller terminalsession för att hämta loggfilerna som en ZIP-fil med hjälp av Azure CLI, och ange följande kommando:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Du måste ha aktiverat filsystemet loggning i Azure Portal eller Azure CLI innan du kör det här kommandot.

## <a name="environment-variables"></a>Miljövariabler

Behåll hemlig information, till exempel nycklar eller token från källkoden av säkerhetsskäl. Använd nycklar och tokens i Funktionskoden genom att läsa dem från miljövariabler.

Om du vill ange miljövariabler när du kör Azure Functions lokalt, kan du lägga till dessa variabler i filen local.settings.json. Om en inte finns i rotkatalogen för din function-projekt, kan du skapa en. Här är hur filen ska se ut:

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
> Om den här metoden är upptaget vara säker att lägga till local.settings.json filen till din lagringsplats Ignorera fil, så att den inte har allokerats.

Med din kod nu beroende på dessa miljövariabler, kan du logga in på Azure portal för att ange samma nyckel / värde-par i din funktionsappinställningar så att din kod equivalently fungerar när du testar lokalt och när du har distribuerat på Azure.

## <a name="next-steps"></a>Nästa steg

Mer information om utveckling av Java för Azure-funktion finns i följande resurser:

* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)
* [Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)
- Lokal utveckling och felsökning med [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md), och [Eclipse](functions-create-maven-eclipse.md). 
* [Fjärrfelsök Java Azure Functions med Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven-pluginprogrammet för Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) -effektivisera funktionsskapande via den `azure-functions:add` mål och Förbered uppsamlingskatalogen för [ZIP-filen distribution](deployment-zip-push.md).
