---
title: Ansluta functions till Azure Storage med hjälp av Visual Studio Code
description: Lär dig hur du lägger till en utdatabindning för att ansluta dina funktioner i en Azure Storage-kö med Visual Studio Code.
author: ggailey777
ms.author: glenga
ms.date: 06/25/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
manager: jeconnoc
ms.openlocfilehash: b207064f691391af2c180c7a6ab03e42ed79fcb6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450998"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio-code"></a>Ansluta functions till Azure Storage med hjälp av Visual Studio Code

Azure Functions kan du ansluta functions till Azure-tjänster och andra resurser utan att skriva egen kod för integrering. Dessa *bindningar*, som representerar både indata och utdata, har deklarerats i definitionen. Data från bindningar har angetts för funktionen som parametrar. En utlösare är en särskild typ av indatabindning. En funktion har bara en utlösare, kan den ha flera indatafiler och utdatabindningar. Mer information finns i [Azure Functions-utlösare och bindningar begrepp](functions-triggers-bindings.md).

Den här artikeln visar hur du använder Visual Studio Code för att ansluta den funktion som du skapade i den [tidigare snabbstartsartikel](functions-create-first-function-vs-code.md) till Azure Storage. Utdatabindningen som du lägger till den här funktionen skriver data från HTTP-begäran till ett meddelande i en Azure Queue storage-kö. 

De flesta bindningar kräver en lagrade anslutningssträng som Functions använder för att komma åt bundna tjänsten. Om du vill göra det enklare, kan du använda det lagringskonto som du skapade med din funktionsapp. Anslutningen till det här kontot lagras redan i en app som inställning med namnet `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här artikeln måste du uppfylla följande krav:

* Installera den [Azure Storage-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).
* Installera [Azure Storage Explorer](https://storageexplorer.com/). Storage Explorer är ett verktyg som du använder för att undersöka Kömeddelanden som genereras av din utdatabindning. Lagringsutforskaren stöds på macOS, Windows och Linux-baserade operativsystem.
* Installera [.NET Core CLI-verktyg](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x) (C# endast-projekt).
* Utför stegen i [del 1 av Visual Studio Code-snabbstarten](functions-create-first-function-vs-code.md). 

Den här artikeln förutsätter att du redan är inloggad på Azure-prenumerationen från Visual Studio Code. Du kan logga in genom att köra `Azure: Sign In` från kommandopaletten. 

## <a name="download-the-function-app-settings"></a>Ladda ned funktionsappinställningarna

I den [tidigare snabbstartsartikel](functions-create-first-function-vs-code.md), du har skapat en funktionsapp i Azure tillsammans med nödvändiga Storage-konto. Anslutningssträngen för det här kontot lagras på ett säkert sätt i appen inställningar i Azure. I den här artikeln får skriva du meddelanden till en lagringskö i samma konto. För att ansluta till ditt Storage-konto när du kör funktionen lokalt, måste du hämta app-inställningar i filen local.settings.json. 

1. Tryck på F1 för att öppna kommandopaletten, och sedan söka efter och kör kommandot `Azure Functions: Download Remote Settings....`. 

1. Välj funktionsappen som du skapade i föregående artikel. Välj **Ja till alla** att skriva över de befintliga lokala inställningarna. 

    > [!IMPORTANT]  
    > Eftersom den innehåller hemligheter, filen local.settings.json aldrig publiceras och exkluderas från källkontroll.

1. Kopiera värdet `AzureWebJobsStorage`, vilket är nyckeln för Storage-konto Anslutningssträngens värde. Du kan använda den här anslutningen för att kontrollera att utdatabindningen fungerar som förväntat.

## <a name="register-binding-extensions"></a>Registrera bindningstillägg

Eftersom du använder en Queue storage-utdatabindning måste du ha Storage-bindningar-tillägget installerat innan du kör projektet. 

### <a name="javascript"></a>JavaScript

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

### <a name="c-class-library"></a>C\# -klassbiblioteket

Med undantag för HTTP och timer utlösare implementeras bindningar som tilläggspaket. Kör följande [dotnet lägga till paketet](/dotnet/core/tools/dotnet-add-package) i terminalfönstret för att lägga till Storage-tillägg-paketet i projektet.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

Nu kan du lägga till storage-utdatabindning i projektet.

## <a name="add-an-output-binding"></a>Lägg till en utdatabindning

I funktioner, varje typ av bindning kräver en `direction`, `type`, och ett unikt `name` definieras i filen function.json. Hur du definierar dessa attribut beror på språket i din funktionsapp.

### <a name="javascript"></a>JavaScript

Bindningen attribut anges direkt i filen function.json. Beroende på bindningstyp av kan eventuellt ytterligare egenskaper som krävs. Den [kö utdata configuration](functions-bindings-storage-queue.md#output---configuration) beskriver de fält som krävs för en bindning för Azure Storage-kö. Tillägget gör det enkelt att lägga till bindningar till filen function.json. 

Att skapa en bindning, högerklicka på (Ctrl + klicka på macOS) den `function.json` filen i mappen HttpTrigger och välj **Lägg till bindning...** . Följ anvisningarna för att definiera följande bindningsegenskaperna för den nya bindningen:

| Uppmaning | Värde | Beskrivning |
| -------- | ----- | ----------- |
| **Välj bindning riktning** | `out` | Bindningen är en utdatabindning. |
| **Välj bindning med riktning...** | `Azure Queue Storage` | Bindningen är en bindning för Azure Storage-kö. |
| **Namnet används för att identifiera den här bindningen i koden** | `msg` | Namn som identifierar bindningsparametern som refereras till i din kod. |
| **Kön som meddelandet ska skickas** | `outqueue` | Namnet på kön som bindningen skriver till. När den *queueName* inte finns några bindningen skapar den första användningen. |
| **Välj inställningen från ”local.setting.json”** | `AzureWebJobsStorage` | Namnet på en programinställning med anslutningssträngen för lagringskontot. Den `AzureWebJobsStorage` inställningen innehåller anslutningssträngen för lagringskontot som du skapade med appen. |

En bindning har lagts till i den `bindings` matris i filen function.json bör nu se ut som i följande exempel:

```json
{
   ...

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

### <a name="c-class-library"></a>C\# -klassbiblioteket

I en C# klassbiblioteksprojektet, bindningarna definieras som bindande attribut för metoden funktion. Function.json-filen är sedan automatiskt genererade baserat på dessa attribut.

Öppna projektfilen HttpTrigger.cs och Lägg till följande `using` instruktionen:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Lägg till följande parameter till den `Run` metoddefinitionen:

```cs
[Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
```

Den `msg` parametern är en `ICollector<T>` typ som representerar en samling av meddelanden som skrivs till en utdatabindning när funktionen har slutförts. I det här fallet utdata är en storage-kö med namnet `outqueue`. Anslutningssträngen för lagringskontot har angetts av den `StorageAccountAttribute`. Det här attributet anger inställningen som innehåller anslutningssträngen för Lagringskonto och kan användas på den klass, metod eller parametern-nivå. I det här fallet kan du utelämna `StorageAccountAttribute` eftersom du redan använder standardkontot för lagring.

Kör metoddefinitionen bör nu se ut så här:  

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
```

## <a name="add-code-that-uses-the-output-binding"></a>Lägg till kod som använder utdatabindning

När bindningen har definierats kan du använda den `name` om bindningen till åtkomst till den som ett attribut i funktionssignaturen. Genom att använda en utdatabindning kan du inte använder Azure Storage SDK-koden för autentisering, lägga till en referens för kön eller skriva data. Functions-körningen och utdata bindning utföra dessa uppgifter åt dig.

### <a name="javascript"></a>JavaScript

Lägg till kod som använder den `msg` utdata binding-objektet på `context.bindings` att skapa ett kömeddelande. Lägg till den här koden före `context.res`-instruktionen.

```javascript
// Add a message to the Storage queue.
context.bindings.msg = "Name passed to the function: " + 
(req.query.name || req.body.name);
```

Din funktion bör nu se ut så här:

```javascript
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

### <a name="c"></a>C\#

Lägg till kod som använder den `msg` bindningsobjekt att skapa ett kömeddelande. Lägg till den här koden innan metoden returneras.

```cs
if (!string.IsNullOrEmpty(name))
{
    // Add a message to the output collection.
    msg.Add(string.Format("Name passed to the function: {0}", name));
}
```

Din funktion bör nu se ut så här:

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
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

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

En ny kö med namnet **outqueue** skapas i ditt lagringskonto av funktionskörningen när utdatabindningen används för första gången. Du använder Storage Explorer för att verifiera att kön har skapats tillsammans med det nya meddelandet.

### <a name="connect-storage-explorer-to-your-account"></a>Anslut Storage Explorer till ditt konto

Hoppa över det här avsnittet om du redan har installerat Azure Storage Explorer och anslutit den till ditt Azure-konto.

1. Kör den [Azure Storage Explorer] verktyget anslutningsikonen till vänster och välj Välj **Lägg till ett konto**.

    ![Lägga till en Azure-konto till Microsoft Azure Lagringsutforskaren](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. I den **Connect** dialogrutan Välj **lägga till ett Azure-konto**, Välj din **Azure-miljön**, och välj **logga in...** . 

    ![Logga in på ditt Azure-konto](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

När du har loggat in på ditt konto kan se du alla Azure-prenumerationer som är associerade med ditt konto.

### <a name="examine-the-output-queue"></a>Granska utdatakö

1. I Visual Studio Code trycker du på F1 för att öppna kommandopaletten, och sedan söka efter och kör kommandot `Azure Storage: Open in Storage Explorer` och välj namnet på ditt Lagringskonto. Ditt lagringskonto öppnas i Azure Storage Explorer.  

1. Expandera noden **Köer** och välj sedan kön med namnet **outqueue**. 

   Kön innehåller meddelandet som köutdatabindningen skapade när du körde den HTTP-utlösta funktionen. Om du startade en funktion med standardvärdet `name` för *Azure*, kommer kömeddelandet att vara *Namn som skickats till funktionen: Azure*.

    ![Kömeddelande som visas i Azure Storage Explorer](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Kör funktionen igen och skicka en annan begäran och ser du ett nytt meddelande i kön.  

Nu är det dags att publicera uppdaterade function-app till Azure.

## <a name="redeploy-and-test-the-updated-app"></a>Distribuera och testa den uppdaterade appen

1. I Visual Studio Code trycker du på F1 för att öppna kommandopaletten. I kommandopaletten, Sök efter och välj `Azure Functions: Deploy to function app...`.

1. Välj funktionsappen som du skapade i den första artikeln. Eftersom du omdistribuerar ditt projekt med samma App, väljer **distribuera** att ignorera varningen om att filerna skrivs över.

1. När distributionen är klar kan du igen använda cURL eller en webbläsare för att testa funktionen omdistribuerade. Lägg till frågesträngen som tidigare `&name=<yourname>` till URL: en, som i följande exempel:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Igen [visas i storage-kö](#examine-the-output-queue) att verifiera att utdatabindning igen genererar ett nytt meddelande i kön.

## <a name="clean-up-resources"></a>Rensa resurser

*Resurser* i Azure refererar till funktionsappar, funktioner, lagringskonton och så vidare. Dessa grupperas i *resursgrupper*. Du kan ta bort allt innehåll i en grupp genom att ta bort gruppen.

Du skapade resurser för att slutföra de här snabbstarterna. Det är möjligt att du debiteras för de här resurserna beroende på din [kontostatus](https://azure.microsoft.com/account/) och dina [servicepriser](https://azure.microsoft.com/pricing/). Om du inte behöver resurserna längre så visar vi hur du tar bort dem här:

1. I Visual Studio Code trycker du på F1 för att öppna kommandopaletten. I kommandopaletten, Sök efter och välj `Azure Functions: Open in portal`.

1. Välj din funktionsapp och tryck på RETUR. Sidan för funktionsappar öppnas i den [Azure-portalen](https://portal.azure.com).

1. I den **översikt** väljer du en namngiven länken under **resursgrupp**.

    ![Välj den resursgrupp som du vill ta bort från sidan för funktionsappar.](./media/functions-add-output-binding-storage-queue-vs-code/functions-app-delete-resource-group.png)

1. Granska listan över resurser som ingår och verifiera att det är dem som du vill ta bort på sidan **Resursgrupp**.
 
1. Välj **Ta bort resursgrupp** och följ instruktionerna.

   Borttagningen kan ta några minuter. När du är färdig visas ett meddelande i några sekunder. Du kan även välja klockikonen längst upp på sidan för att se meddelandet.

## <a name="next-steps"></a>Nästa steg

Du har uppdaterat din HTTP-utlöst funktion för att skriva data till en lagringskö. Mer information om hur du utvecklar funktioner finns [utveckla Azure-funktioner med hjälp av Visual Studio Code](functions-develop-vs-code.md).

Därefter bör du aktivera Application Insights-övervakning för din funktionsapp:

> [!div class="nextstepaction"]
> [Aktivera Application Insights-integrering](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/
